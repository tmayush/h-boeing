<template>
  <div>
    <div ref="earf" />
  </div>
</template>

<script>
  import * as THREE from '@/../node_modules/three/build/three.module'
  import { onMounted } from '@vue/composition-api'

  export default {
    name: 'Earf',
    setup (_, ctx) {
      let camera, scene, renderer
      let world

      onMounted(() => {
        init()
        animate()
        ctx.refs.earf.appendChild(renderer.domElement)
      })

      const init = () => {
        camera = new THREE.PerspectiveCamera(45, 1, 0.01, 1000)
        camera.position.z = 1.4

        scene = new THREE.Scene()

        scene.add(new THREE.AmbientLight(0x333333))

        const light = new THREE.DirectionalLight(0xffffff, 1)
        light.position.set(5, 3, 5)
        scene.add(light)

        const noClouds = require('@/assets/2_no_clouds_4k.jpg')
        const bumpMap = require('@/assets/elev_bump_4k.jpg')
        const specMap = require('@/assets/water_4k.png')

        world = new THREE.Mesh(
          new THREE.SphereGeometry(0.5, 32, 32),
          new THREE.MeshPhongMaterial({
            map: new THREE.TextureLoader().load(noClouds),
            bumpMap: new THREE.TextureLoader().load(bumpMap),
            bumpScale: 0.005,
            specularMap: new THREE.TextureLoader().load(specMap),
            specular: 0x222222
          })
        )

        scene.add(world)

        renderer = new THREE.WebGLRenderer({
          antialias: true,
          alpha: true
        })
        renderer.setClearColor(0x000000, 0)
        renderer.setPixelRatio(window.devicePixelRatio)

        if (window.innerWidth < 768) {
          renderer.setSize(12.5 * 16, 12.5 * 16)
        } else if (window.innerWidth < 1023) {
          renderer.setSize(18 * 16, 18 * 16)
        } else {
          renderer.setSize(21 * 16, 21 * 16)
        }

        window.addEventListener('resize', onWindowResize, false)
      }

      const onWindowResize = () => {
        if (window.innerWidth < 768) {
          renderer.setSize(13 * 16, 13 * 16)
        } else if (window.innerWidth < 1023) {
          renderer.setSize(18 * 16, 18 * 16)
        } else {
          renderer.setSize(21 * 16, 21 * 16)
        }
      }

      const animate = () => {
        requestAnimationFrame(animate)
        world.rotation.y += 0.0035
        renderer.render(scene, camera)
      }

      return {
      }
    }
  }
</script>
