<template>
  <div id="app">
    <a-scene physics="debug: false">
      <a-assets>
        <a-asset-item id="face-json" src="./models/face.json"></a-asset-item>
        <a-asset-item id="flesh-json" src="./models/flesh.json"></a-asset-item>
        <a-asset-item id="laser-json" src="./models/laser.json"></a-asset-item>
        <a-asset-item id="chainsaw-json" src="./models/chainsaw.json"></a-asset-item>
        <a-asset-item id="hand-laser-json" src="./models/hand-laser.json"></a-asset-item>
        <a-asset-item id="circular-saw-json" src="./models/circular-saw.json"></a-asset-item>
        <a-asset-item id="sawzall-json" src="./models/sawzall.json"></a-asset-item>
        <a-asset-item id="head-json" src="./models/rigged-head.json"></a-asset-item>
      </a-assets>

      <!-- Floor -->
      <a-plane static-body position='0 -2 0' height='100' width='100' color='#a3b3c3' rotation='-90 0 0'></a-plane>

      <!-- Immovable box -->
      <!-- <a-box static-body position="0 0.5 -5" width="3" height="1" depth="1"></a-box> -->

      <!-- Dynamic box -->
      <a-box dynamic-body position="3 0.5 -4" width="1" height="1" depth="1"></a-box>

      <!-- <a-entity id="csaw" class="tool" circular-saw
        scale='1 1 1' rotation='0 0 0' position='0 2 -10'
        json-model="src: #circular-saw-json;">
        <a-animation attribute='rotation'
          dur='10000'
          fill='none'
          to='0 360 0'
          repeat='indefinite'
          easing='linear'></a-animation>
      </a-entity> -->

      <a-entity dynamic-body="shape: sphere; mass: 500; sphereRadius: 2.0; linearDamping: 0.99; angularDamping: 0.99; " position='0 0 -10' scale='2 2 2'>
        <a-entity geometry="primitive: box;"></a-entity>
        <a-entity id="head" class="target" head position='0 -1 0'
          json-model="src: #head-json;">
        </a-entity>
      </a-entity>

      <a-entity id='eyes-target' controller geometry='primitive: sphere' position='-5 5 -2' rotation='0 180 0'>

        <a-entity id='laser' laser
        scale='1 1 1' position='0 0 0' rotation='0 0 0'
        animation-mixer="clip: omg;"
        json-model="src: #hand-laser-json;">

          <a-entity beam static-body geometry='primitive: box;' scale='0.1 0.1 10' position='0 0 7'></a-entity>

        </a-entity>
        <a-animation attribute='position'
          dur='1000'
          fill='none'
          to='5 -5 -2'
          repeat='indefinite'
          easing='linear'></a-animation>
      </a-entity>

      <!-- <a-entity id="szall" class="tool" sawzall
        scale='0.5 0.5 0.5' rotation='0 0 0' position='3 2 -10'
        json-model="src: #sawzall-json;">
        <a-animation attribute='rotation'
          dur='10000'
          fill='none'
          to='0 360 0'
          repeat='indefinite'
          easing='linear'></a-animation>
      </a-entity> -->



      <!-- <a-entity particle-system></a-entity> -->

    </a-scene>
  </div>
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      started: true
    }
  },
  beforeCreate () {
    AFRAME.registerComponent('controller', {
      init: function () {
        console.log('loaded controller')
      },
      tick: function(){
        return
      }
    });

    AFRAME.registerComponent('beam', {
      init: function () {
        console.log('loaded beam', this.el)
        window.z = this.el
        this.el.addEventListener('collide', function(e){
          console.log('collide beam E_E')
          console.log(e)
          console.log(e.detail.target.el)  // Original entity (playerEl).
          console.log('what did the beam touch?', e.detail.body.el)    // Other entity, which playerEl touched.
          var event = new Event('zap')
          e.detail.body.el.dispatchEvent(event)
          console.log(e.detail.contact)    // Stats about the collision (CANNON.ContactEquation).
          console.log(e.detail.contact.ni) // Normal (direction) of the collision (CANNON.Vec3).
        })
      },
      tick: function(){
        // this.el.components['position'] = new THREE.Vector3(0,0,0)
        return
      }
    });


    AFRAME.registerComponent('head', {
      init: function () {
        var self = this
        window.head = this.el
        self.eyes_target = new THREE.Vector3(0, 0, -5)
        self.eyes_target_entity = {}
        this.el.addEventListener('model-loaded', function () {
          self.eyes_target_entity = document.querySelector('#eyes-target').components.position.attrValue
          console.log('head load')
          self.bones = self.el.object3D.children[0].skeleton.bones
          console.log('bones', self.bones)
          self.bones.forEach(function(b){
            b.rest = {
              position: new THREE.Vector3(b.position.x, b.position.y, b.position.z),
              rotation: new THREE.Vector3(b.rotation.x, b.rotation.y, b.rotation.z),
              scale: new THREE.Vector3(b.scale.x, b.scale.y, b.scale.z),
            }
          })
          // window.k = self.bones
          self.el.parentEl.addEventListener('zap', function(evt){
            console.log('got zapped')
            var multi = 3.5
            self.bones.forEach(function(b, i){
              if(i > 0){
                b.rotation.x += multi * (Math.random() - 0.5)
                b.rotation.y += multi * (Math.random() - 0.5)
                b.rotation.z += multi * (Math.random() - 0.5)
                b.scale.x += Math.abs(0.1 * multi * (Math.random() - 0.5))
                b.scale.y += Math.abs(0.1 * multi * (Math.random() - 0.5))
                b.scale.z += Math.abs(0.1 * multi * (Math.random() - 0.5))
              }
            })
          })
          document.addEventListener('keypress', function(evt){
            if(evt.code === 'Space'){
              var multi = 3.5
              self.bones.forEach(function(b, i){
                if(i > 0){
                  b.rotation.x += multi * (Math.random() - 0.5)
                  b.rotation.y += multi * (Math.random() - 0.5)
                  b.rotation.z += multi * (Math.random() - 0.5)
                  b.scale.x += Math.abs(0.1 * multi * (Math.random() - 0.5))
                  b.scale.y += Math.abs(0.1 * multi * (Math.random() - 0.5))
                  b.scale.z += Math.abs(0.1 * multi * (Math.random() - 0.5))
                }
              })
              // self.toggle_status()
            }
          })
        });

        this.el.addEventListener('collide', function(evt){
          console.log('collide head')
          // console.log(evt)
        })


        // this.el.setObject3D('particles', createParticleSystem());
      },
      tick: function(){
        // console.log('tick')
        // return
        var self = this

        // var t = document.querySelector('#eyes-target').components.position.attrValue
        self.eyes_target = new THREE.Vector3(self.eyes_target_entity.x, self.eyes_target_entity.y, self.eyes_target_entity.z)
        if(self.bones !== undefined){
          // console.log('tick')
          // look at object
          window.m = self.bones[4]
          // set the rotation of the eyes based on the eyes target
          if(self.eyes_target.x > 0){
            self.bones[4].rotation.y = Math.sqrt(Math.abs(self.eyes_target.x)) * 0.1
            self.bones[5].rotation.y = Math.sqrt(Math.abs(self.eyes_target.x)) * 0.12
          } else {
            self.bones[4].rotation.y = Math.sqrt(Math.abs(self.eyes_target.x)) * -0.12
            self.bones[5].rotation.y = Math.sqrt(Math.abs(self.eyes_target.x)) * -0.1
          }

          if(self.eyes_target.y < 0){
            self.bones[4].rotation.x = Math.sqrt(Math.abs(self.eyes_target.y)) * 0.1
            self.bones[5].rotation.x = Math.sqrt(Math.abs(self.eyes_target.y)) * 0.1
          } else {
            self.bones[4].rotation.x = Math.sqrt(Math.abs(self.eyes_target.y)) * -0.1
            self.bones[5].rotation.x = Math.sqrt(Math.abs(self.eyes_target.y)) * -0.1
          }

          var _t = Date.now() * 0.00001
          var multi = 0.03
          var scale_multi = 0.01
          self.bones.forEach(function(b,i){
            if(i > 0){
              b.rotation.x += multi * (b.rest.rotation.x - b.rotation.x)
              b.rotation.y += multi * (b.rest.rotation.y - b.rotation.y)
              b.rotation.z += multi * (b.rest.rotation.z - b.rotation.z)
              b.scale.x += scale_multi * (b.rest.scale.x - b.scale.x)
              b.scale.y += scale_multi * (b.rest.scale.y - b.scale.y)
              b.scale.z += scale_multi * (b.rest.scale.z - b.scale.z)
            }
          })
        }
      }
    });
    AFRAME.registerComponent('particle-system', {
      init: function () {
        this.el.setObject3D('particles', createParticleSystem());
        console.log('loaded particle system')
      },
      tick: function(){
        // console.log('tick')
        return
        var m = this.el.getObject3D('particles')
        m.geometry.vertices.forEach(function(o){
          o.x += Math.random()
        })
        m.geometry.verticesNeedUpdate = true
      }
    });
    AFRAME.registerComponent('circular-saw', {
      init: function () {
        console.log('circular saw init')
        var el = this.el;
        var assetEl = this.data.src;
        var self = this

        self.status = 'off'
        self.rotation_velocity = 0
        self.rotation_speedlimit = 3
        self.toggle_status = function(){
          if(self.status === 'off'){
            self.status = 'on'
            if(self.bones !== undefined){
              self.bones[0].position.y -= 0.07
            }
          } else {
            self.status = 'off'
            if(self.bones !== undefined){
              self.bones[0].position.y += 0.07
            }
          }
        }
        el.addEventListener('model-loaded', function () {
          console.log('circular saw load')
          self.bones = self.el.object3D.children[0].skeleton.bones
          console.log('bones', self.bones)
          // window.k = self.bones
          document.addEventListener('keypress', function(evt){
            if(evt.code === 'Space'){
              self.toggle_status()
            }
          })
        });
      },
      tick: function(){
        if(this.status === 'on'){
          if(this.bones !== undefined){
            if(this.rotation_velocity < this.rotation_speedlimit){
              this.rotation_velocity += 0.03
            }
          }
        } else {
          this.rotation_velocity *= 0.95
        }
        if(this.rotation_velocity > 0.001){
          this.bones[1].rotation.x -= this.rotation_velocity
        } else {
          this.rotation_velocity = 0
        }

      }
    });


    AFRAME.registerComponent('laser', {
      init: function () {
        console.log('laser init')
        var el = this.el;
        var assetEl = this.data.src;
        var self = this

        self.status = 'off'
        self.laser = {}
        self.toggle_status = function(){
          if(self.status === 'off'){
            self.status = 'on'
            if(self.bones !== undefined){
              self.bones[0].position.z += 0.07
              self.laser.visible = true
            }
          } else {
            self.status = 'off'
            if(self.bones !== undefined){
              self.bones[0].position.z -= 0.07
              self.laser.visible = false
            }
          }
        }
        el.addEventListener('model-loaded', function () {
          console.log('laser load')
          console.log(self.el.object3D)
          self.bones = self.el.object3D.children[1].skeleton.bones
          self.laser = self.el.object3D.children[0]
          // console.log('bones', self.bones)
          window.k = self.bones
          self.laser.visible = false
          document.addEventListener('keypress', function(evt){
            if(evt.code === 'Space'){
              self.toggle_status()
            }
          })
        });
      },
      tick: function(){
        if(this.status === 'on'){
          if(this.bones !== undefined){
          }
        } else {
        }
      }
    });






    AFRAME.registerComponent('sawzall', {
      init: function () {
        console.log('sawzall saw init')
        var el = this.el;
        var assetEl = this.data.src;
        var self = this

        self.status = 'off'
        self.jiggle_velocity = 0.1
        self.jiggle_direction = 1
        self.rotation_speedlimit = 3
        self.blade_bone_root = -1
        self.toggle_status = function(){
          if(self.status === 'off'){
            self.status = 'on'
            if(self.bones !== undefined){
              // self.bones[1].position.y -= 0.07
              self.bones[1].position.z += 0.15
              self.jiggle_velocity = 0.1
            }
          } else {
            self.status = 'off'
            if(self.bones !== undefined){
              // self.bones[1].position.y += 0.07
              self.bones[1].position.z -= 0.15
              console.log('off')
            }
          }
        }
        el.addEventListener('model-loaded', function () {
          console.log('sawzall saw load')
          self.bones = self.el.object3D.children[0].skeleton.bones
          console.log('bones', self.bones)
          window.k = self.bones
          self.blade_bone_root = self.bones[2].position.z
          document.addEventListener('keypress', function(evt){
            if(evt.code === 'Space'){
              self.toggle_status()
            }
          })
        });
      },
      tick: function(){
        var self = this
        if(this.status === 'on'){
          if(this.bones !== undefined){
            // jiggle saw
            self.bones[2].position.z += self.jiggle_direction * self.jiggle_velocity
            if(self.bones[2].position.z < (self.blade_bone_root - 0.5)){
              self.jiggle_direction *= -1
              self.bones[2].position.z = (self.blade_bone_root - 0.5)
            } else if (self.bones[2].position.z > self.blade_bone_root) {
              self.jiggle_direction *= -1
              self.bones[2].position.z = self.blade_bone_root
            }
          }
        } else {

        }
      }
    });
    // AFRAME.registerComponent('collider-check', {
    //   init: function () {
    //     console.log('adding event listener')
    //     this.el.addEventListener('raycaster-intersected', function (evt) {
    //       // console.log('Player hit something!');
    //       window.evt = evt
    //       // console.log(evt.detail.intersection)
    //       if(evt.detail.intersection.faceIndex !== undefined){
    //         // console.log(this.object3D.children[0].geometry.faces[evt.detail.intersection.faceIndex].materialIndex = 2)
    //         this.object3D.children[0].geometry.faces[evt.detail.intersection.faceIndex].materialIndex = 2
    //         this.object3D.children[0].geometry.groupsNeedUpdate = true
    //       }
    //       var a = evt.detail.intersection.point
    //       // document.querySelector('#hit-indicator').object3D.position.set(a.x, a.y, a.z)
    //     });
    //   }
    // });
  },
  mounted () {
    var self = this
    // window.k = document.querySelector('#laser')
    // window.m = window.k.object3D
    // console.log(window.k.object3D)

    setTimeout(function(){
      var raycasterEl = AFRAME.scenes[0].querySelector('[raycaster]');
      if(raycasterEl){
        raycasterEl.components.raycaster.refreshObjects();
      }
      // k.components['animation-mixer'].activeActions.forEach(function(p){
      //   console.log(p)
      // })

    }, 1000)

    // var p = createParticleSystem()
    // console.log(p)
    // AFRAME.scenes[0].add(p)
    //
    //   var r = new THREE.Raycaster(new THREE.Vector3(0,0,0), new THREE.Vector3(0, 1, 0), 0, 20);
    //   var m = r.intersectObject(AFRAME.scenes[0].children['face'].object3D, true)
    //   console.log(m)
    //   if(m.length > 0){
    //     m.forEach(function(k){
    //       console.log(k.face.materialIndex)
    //       k.face.materialIndex = 1
    //       k.object.geometry.groupsNeedUpdate = true
    //     })
    //   }
    // }, 1000)

    // console.log(k.components['animation-mixer'])
    // k.addEventListener('loaded', function(){
    //   setTimeout(function(){
    //     console.log('here')
    //     console.log('object3d', k.object3D)
    //     console.log('children', k.object3D.children)
    //     var a = k.object3D.children[0].geometry.animations[0]
    //     //console.log(a)
    //   },100)
    // })

    // activate individual clip actions
    // var a = k.object3D.children[0].geometry.animations[0]
    // var m = k.components['animation-mixer'].mixer.clipAction(a)
    // m.clampWhenFinished = true; m.repetitions = 0; m.play()
    // m.play()

    // var a = k.object3D.children[0].geometry.animations[0]; var m = k.components['animation-mixer'].mixer.clipAction(a);
    // m.clampWhenFinished = true; m.repetitions = 0; m.play()


    // access individual materials
    // k.object3D.children[0].geometry.faces.forEach(function(m){ m.materialIndex = 2 }); k.object3D.children[0].geometry.groupsNeedUpdate = true

    // access individual bones
    // var bones = k.object3D.children[0].skeleton.bones

  }
}

function createParticleSystem() {

    // The number of particles in a particle system is not easily changed.
    var particleCount = 2000;

    // Particles are just individual vertices in a geometry
    // Create the geometry that will hold all of the vertices
    var particles = new THREE.Geometry();

    // Create the vertices and add them to the particles geometry
    for (var p = 0; p < particleCount; p++) {

        // This will create all the vertices in a range of -200 to 200 in all directions
        var x = Math.random() * 400 - 200;
        var y = Math.random() * 400 - 200;
        var z = Math.random() * 400 - 200;

        // Create the vertex
        var particle = new THREE.Vector3(x, y, z);

        // Add the vertex to the geometry
        particles.vertices.push(particle);
    }

    // Create the material that will be used to render each vertex of the geometry
    var particleMaterial = new THREE.PointsMaterial(
            {color: 0xff0000,
             size: 4,
            //  map: THREE.ImageUtils.loadTexture("images/snowflake.png"),
             blending: THREE.AdditiveBlending,
             transparent: true,
            });

    // Create the particle system
    var particleSystem = new THREE.Points(particles, particleMaterial);

    return particleSystem;
}
</script>
