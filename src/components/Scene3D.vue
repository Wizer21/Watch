<template>
  <canvas id="watchScene">
  </canvas>
</template>

<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'

export default {
  name: 'scene3D',
  mounted(){
    const canvas = document.getElementById('watchScene')

    let sizes = {}

    // render
    const renderer = new THREE.WebGLRenderer({
      canvas: canvas
    })
    renderer.setSize(sizes.width, sizes.height * 2)
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
		renderer.setScissorTest(true)

    // loader 
    const gltfLoader = new GLTFLoader()
    
    // camera 
    const camera = new THREE.PerspectiveCamera(75, 1, 0.01, 20)
    camera.position.z = 4
    camera.lookAt(0, 0, 0)
    
    // Set Default Sizes
    setSizes()

    // light 
    const ambientLight = new THREE.AmbientLight( 0xffffff, 2)
    const pointLight = new THREE.PointLight( 0xffffff, 3, 20)
    pointLight.position.set(-6, 0, 2)

    // ----------
    // PART 1 
    // ----------
    const scene1 = new THREE.Scene()
    scene1.add(ambientLight)
    scene1.add(pointLight)

    // mesh
    gltfLoader.load('./watch.glb', gltf => {
      const watch = gltf.scene

      watch.scale.set(0.6, 0.6, 0.6)
      
      watch.rotation.x = Math.PI * 0.5
      watch.rotation.y = Math.PI * 0.95
      watch.rotation.z = - Math.PI * 0.2

      scene1.add(watch)
    })

    // ----------
    // PART 2
    // ----------
    const scene2 = new THREE.Scene()

    // mesh
    const cubeRed =  new THREE.Mesh(
      new THREE.BoxGeometry(1, 1, 1),
      new THREE.MeshBasicMaterial({ color: 0xff0000})
    )
    scene2.add(cubeRed)

    const tick = () => {
      
      // Scissor Scenes
      renderer.setScissor(0, 0, sizes.width, sizes.height)
      renderer.setViewport(0, 0, sizes.width, sizes.height)
      renderer.render(scene1, camera)

      renderer.setScissor(0, sizes.height, sizes.width, sizes.height)
      renderer.setViewport(0, sizes.height, sizes.width, sizes.height)
      renderer.render(scene2, camera)

      window.requestAnimationFrame(tick)
    }
    tick()

    //Camera MouseMove
    document.body.addEventListener('mousemove', event => {
      let x = event.clientX - (sizes.width / 2)
      let y = event.clientY - (sizes.height / 2)

      const amplitude = 1

      const xRatio = (sizes.width / 2) / amplitude
      const yRatio = (sizes.height / 2) / amplitude

      camera.position.x = -x /xRatio
      camera.position.y = y /yRatio

      camera.lookAt(0, 0, 0)
    })

    function setSizes(){
      sizes = {
        height: window.innerHeight,
        width: window.innerWidth
      }

      // Update camera
      camera.aspect = sizes.width / sizes.height
      camera.updateProjectionMatrix()

      // Update renderer
      renderer.setSize(sizes.width, sizes.height * 2)
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
    }

    // Resize
    window.addEventListener('resize', () =>
    {
      setSizes()
    })
  }
}
</script>

<style scoped>
</style>
