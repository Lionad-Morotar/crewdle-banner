<template>
  <div ref="container" class="sphere-scene" aria-hidden="true"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import * as THREE from 'three'

/**
 * 使用 Three.js 创建真正的 3D 虹彩形变球体。
 * - MeshPhysicalMaterial 提供真实金属/清漆反射
 * - 自定义 CubeTexture 环境贴图控制紫色/青绿/橙色/高光区域
 * - 顶点噪声让球体形态缓慢变化
 */
const container = ref<HTMLDivElement | null>(null)

let renderer: THREE.WebGLRenderer | null = null
let scene: THREE.Scene | null = null
let camera: THREE.PerspectiveCamera | null = null
let sphere: THREE.Mesh | null = null
let material: THREE.MeshPhysicalMaterial | null = null
let rafId = 0

// Simplex noise for organic displacement
const snoise = `
  vec3 mod289(vec3 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
  vec4 mod289(vec4 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
  vec4 permute(vec4 x) { return mod289(((x * 34.0) + 1.0) * x); }
  vec4 taylorInvSqrt(vec4 r) { return 1.79284291400159 - 0.85373472095314 * r; }
  float snoise(vec3 v) {
    const vec2 C = vec2(1.0 / 6.0, 1.0 / 3.0);
    const vec4 D = vec4(0.0, 0.5, 1.0, 2.0);
    vec3 i  = floor(v + dot(v, C.yyy));
    vec3 x0 = v - i + dot(i, C.xxx);
    vec3 g = step(x0.yzx, x0.xyz);
    vec3 l = 1.0 - g;
    vec3 i1 = min(g.xyz, l.zxy);
    vec3 i2 = max(g.xyz, l.zxy);
    vec3 x1 = x0 - i1 + C.xxx;
    vec3 x2 = x0 - i2 + C.yyy;
    vec3 x3 = x0 - D.yyy;
    i = mod289(i);
    vec4 p = permute(permute(permute(
              i.z + vec4(0.0, i1.z, i2.z, 1.0))
            + i.y + vec4(0.0, i1.y, i2.y, 1.0))
            + i.x + vec4(0.0, i1.x, i2.x, 1.0));
    float n_ = 0.142857142857;
    vec3 ns = n_ * D.wyz - D.xzx;
    vec4 j = p - 49.0 * floor(p * ns.z * ns.z);
    vec4 x_ = floor(j * ns.z);
    vec4 y_ = floor(j - 7.0 * x_);
    vec4 x = x_ * ns.x + ns.yyyy;
    vec4 y = y_ * ns.x + ns.yyyy;
    vec4 h = 1.0 - abs(x) - abs(y);
    vec4 b0 = vec4(x.xy, y.xy);
    vec4 b1 = vec4(x.zw, y.zw);
    vec4 s0 = floor(b0) * 2.0 + 1.0;
    vec4 s1 = floor(b1) * 2.0 + 1.0;
    vec4 sh = -step(h, vec4(0.0));
    vec4 a0 = b0.xzyw + s0.xzyw * sh.xxyy;
    vec4 a1 = b1.xzyw + s1.xzyw * sh.zzww;
    vec3 p0 = vec3(a0.xy, h.x);
    vec3 p1 = vec3(a0.zw, h.y);
    vec3 p2 = vec3(a1.xy, h.z);
    vec3 p3 = vec3(a1.zw, h.w);
    vec4 norm = taylorInvSqrt(vec4(dot(p0,p0), dot(p1,p1), dot(p2,p2), dot(p3,p3)));
    p0 *= norm.x; p1 *= norm.y; p2 *= norm.z; p3 *= norm.w;
    vec4 m = max(0.6 - vec4(dot(x0,x0), dot(x1,x1), dot(x2,x2), dot(x3,x3)), 0.0);
    m = m * m;
    return 42.0 * dot(m * m, vec4(dot(p0,x0), dot(p1,x1), dot(p2,x2), dot(p3,x3)));
  }
`

const vertexShader = `
  uniform float uTime;
  varying vec3 vNormal;
  varying vec3 vPosition;
  ${snoise}
  void main() {
    float displacement = snoise(position * 0.9 + uTime * 0.12) * 0.045;
    displacement += snoise(position * 2.0 + uTime * 0.08) * 0.018;
    vec3 newPosition = position + normal * displacement;
    vPosition = newPosition;
    vNormal = normalize(normalMatrix * normal);
    gl_Position = projectionMatrix * modelViewMatrix * vec4(newPosition, 1.0);
  }
`

function createEnvironmentMap(): THREE.CubeTexture {
  const size = 512
  const faces: HTMLCanvasElement[] = []

  // Face orientations: px, nx, py, ny, pz, nz
  // We'll paint colored reflection zones per face to mimic the reference lighting.
  const faceDefs = [
    { name: 'px', base: [15, 10, 8], zones: [] as any[] },
    { name: 'nx', base: [12, 9, 7], zones: [{ color: [110, 65, 160], pos: [0.25, 0.35], radius: 0.35, intensity: 0.9 }] },
    { name: 'py', base: [14, 10, 8], zones: [{ color: [255, 250, 240], pos: [0.65, 0.35], radius: 0.18, intensity: 1.0 }] },
    { name: 'ny', base: [10, 8, 6], zones: [{ color: [200, 95, 40], pos: [0.5, 0.45], radius: 0.4, intensity: 0.7 }] },
    { name: 'pz', base: [13, 9, 7], zones: [{ color: [70, 165, 130], pos: [0.7, 0.3], radius: 0.28, intensity: 0.75 }] },
    { name: 'nz', base: [8, 6, 5], zones: [] as any[] },
  ]

  for (const face of faceDefs) {
    const canvas = document.createElement('canvas')
    canvas.width = size
    canvas.height = size
    const ctx = canvas.getContext('2d')!

    // Dark base
    ctx.fillStyle = `rgb(${face.base[0]}, ${face.base[1]}, ${face.base[2]})`
    ctx.fillRect(0, 0, size, size)

    // Soft gradient vignette to black at edges
    const grad = ctx.createRadialGradient(size / 2, size / 2, size * 0.2, size / 2, size / 2, size * 0.75)
    grad.addColorStop(0, `rgba(${face.base[0]}, ${face.base[1]}, ${face.base[2]}, 0)`)
    grad.addColorStop(1, 'rgba(5, 4, 3, 0.85)')
    ctx.fillStyle = grad
    ctx.fillRect(0, 0, size, size)

    // Colored zones
    for (const zone of face.zones) {
      const zx = zone.pos[0] * size
      const zy = zone.pos[1] * size
      const zr = zone.radius * size
      const zg = ctx.createRadialGradient(zx, zy, zr * 0.1, zx, zy, zr)
      zg.addColorStop(0, `rgba(${zone.color[0]}, ${zone.color[1]}, ${zone.color[2]}, ${zone.intensity})`)
      zg.addColorStop(0.5, `rgba(${zone.color[0]}, ${zone.color[1]}, ${zone.color[2]}, ${zone.intensity * 0.4})`)
      zg.addColorStop(1, `rgba(${zone.color[0]}, ${zone.color[1]}, ${zone.color[2]}, 0)`)
      ctx.fillStyle = zg
      ctx.fillRect(0, 0, size, size)
    }

    faces.push(canvas)
  }

  const cube = new THREE.CubeTexture(faces)
  cube.needsUpdate = true
  return cube
}

onMounted(() => {
  if (!container.value) return

  const width = container.value.clientWidth
  const height = container.value.clientHeight

  scene = new THREE.Scene()
  camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 100)
  camera.position.z = 4.6

  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(width, height)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
  renderer.toneMapping = THREE.ACESFilmicToneMapping
  renderer.toneMappingExposure = 0.85
  container.value.appendChild(renderer.domElement)

  const envMap = createEnvironmentMap()
  scene.environment = envMap

  const geometry = new THREE.SphereGeometry(1.35, 256, 256)
  material = new THREE.MeshPhysicalMaterial({
    color: new THREE.Color(0x0c0907),
    emissive: new THREE.Color(0x030201),
    metalness: 0.92,
    roughness: 0.18,
    clearcoat: 0.85,
    clearcoatRoughness: 0.18,
    iridescence: 0.65,
    iridescenceIOR: 1.3,
    iridescenceThicknessRange: [120, 400],
    envMap,
    envMapIntensity: 1.45,
  })

  // Custom shader injection for vertex displacement
  material.onBeforeCompile = (shader) => {
    shader.uniforms.uTime = { value: 0 }
    shader.vertexShader = `
      uniform float uTime;
      ${snoise}
    ` + shader.vertexShader.replace(
      '#include <begin_vertex>',
      `
      #include <begin_vertex>
      float displacement = snoise(position * 0.9 + uTime * 0.12) * 0.045;
      displacement += snoise(position * 2.0 + uTime * 0.08) * 0.018;
      transformed += normal * displacement;
      `
    )
    material!.userData.shader = shader
  }

  sphere = new THREE.Mesh(geometry, material)
  sphere.rotation.y = -Math.PI * 0.75
  sphere.scale.setScalar(1.0)
  scene.add(sphere)

  // Fill lights
  const ambient = new THREE.AmbientLight(0xffffff, 0.04)
  scene.add(ambient)

  const mainLight = new THREE.DirectionalLight(0xfff5e6, 0.6)
  mainLight.position.set(2.5, 2.5, 3.0)
  scene.add(mainLight)

  const clock = new THREE.Clock()
  const animate = () => {
    rafId = requestAnimationFrame(animate)
    const elapsed = clock.getElapsedTime()
    if (material?.userData.shader) {
      material.userData.shader.uniforms.uTime.value = elapsed
    }
    if (sphere) {
      sphere.rotation.y += 0.0008
      sphere.rotation.z += 0.0003
    }
    if (renderer && scene && camera) {
      renderer.render(scene, camera)
    }
  }
  animate()

  const handleResize = () => {
    if (!container.value || !camera || !renderer) return
    const w = container.value.clientWidth
    const h = container.value.clientHeight
    camera.aspect = w / h
    camera.updateProjectionMatrix()
    renderer.setSize(w, h)
  }
  window.addEventListener('resize', handleResize)

  onUnmounted(() => {
    window.removeEventListener('resize', handleResize)
    cancelAnimationFrame(rafId)
    if (renderer) {
      renderer.dispose()
      if (container.value && renderer.domElement.parentNode === container.value) {
        container.value.removeChild(renderer.domElement)
      }
    }
    geometry.dispose()
    material?.dispose()
    envMap.dispose()
  })
})
</script>

<style scoped>
.sphere-scene {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  pointer-events: none;
  overflow: hidden;
}

.sphere-scene canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
