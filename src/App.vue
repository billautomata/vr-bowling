<template>
  <div id="app">
    <a-scene physics="debug: true;" shadows>
      <a-assets>
      </a-assets>
      <a-camera position='0 0 15' look-controls></a-camera>
      <!-- Floor -->
      <a-plane static-body position='0 -2 0' height='150' width='150' color='#a3b3c9' rotation='-90 0 0'></a-plane>
      <!-- Dynamic box -->
      <!-- <a-box dynamic-body position="0 0 -10" width="1" height="1" depth="1"></a-box>
      <a-box dynamic-body position="0 0 -11" width="1" height="2" depth="1"></a-box> -->

      <a-box id='lane' static-body position='0 -2 -10' height='0.1' width='10' depth='40' color='#bada55' rotation='0 0 0'></a-box>
      <a-box id='backpane' static-body position='0 3 -30' height='10' width='14' depth='1' color='#00FFF4' rotation='0 0 0'></a-box>
      <a-box id='gutter-left' static-body position='-6 -2.1 -10' height='0.21' width='2' depth='40' color='#FF0000' rotation='0 0 0'></a-box>
      <a-box id='gutter-left-wall' static-body position='-7 -1.5 -10' height='1' width='0.1' depth='40' color='#FFFF00' rotation='0 0 0'></a-box>

      <a-box id='gutter-right' static-body position='6 -2.1 -10' height='0.21' width='2' depth='40' color='#FF0000' rotation='0 0 0'></a-box>
      <a-box id='gutter-right-wall' static-body position='7 -1.5 -10' height='1' width='0.1' depth='40' color='#FFFF00' rotation='0 0 0'></a-box>

      <a-box class='pins' id='pin1' pin dynamic-body position="0 2 -15" width="1" height="4" depth="1" color='#16F'></a-box>

      <a-box class='pins' id='pin2' pin dynamic-body position="1 2 -17" width="1" height="4" depth="1" color='#26F'></a-box>
      <a-box class='pins' id='pin3' pin dynamic-body position="-1 2 -17" width="1" height="4" depth="1" color='#36F'></a-box>

      <a-box class='pins' id='pin4' pin dynamic-body position="-2 2 -19" width="1" height="4" depth="1" color='#46F'></a-box>
      <a-box class='pins' id='pin5' pin dynamic-body position="0 2 -19" width="1" height="4" depth="1" color='#36F'></a-box>
      <a-box class='pins' id='pin6' pin dynamic-body position="2 2 -19" width="1" height="4" depth="1" color='#46F'></a-box>

      <a-box class='pins' id='pin7' pin dynamic-body position="-3 2 -21" width="1" height="4" depth="1" color='#56F'></a-box>
      <a-box class='pins' id='pin8' pin dynamic-body position="-1 2 -21" width="1" height="4" depth="1" color='#66F'></a-box>
      <a-box class='pins' id='pin9' pin dynamic-body position="1 2 -21" width="1" height="4" depth="1" color='#34F'></a-box>
      <a-box class='pins' id='pin10' pin dynamic-body position="3 2 -21" width="1" height="4" depth="1" color='#35F'></a-box>

      <a-sphere id='ball' dynamic-body='mass: 100;' position="0.5 0 10" radius='1'></a-sphere>
      <a-entity vive-controls="hand: left"></a-entity>
      <a-entity vive-controls="hand: right"></a-entity>
    </a-scene>
  </div>
</template>

<script>
require('./pin-component.js')

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
  },
  mounted () {
    var self = this
    setTimeout(function(){
      var el = AFRAME.scenes[0].querySelector('#ball');
      el.body.velocity.z -= 20.0
      el.body.velocity.x -= 10.0 * (Math.random() - 0.5)
      // el.body.applyImpulse(
      //   /* impulse */        new CANNON.Vec3(0, 0, -500),
      //   /* world position */ new CANNON.Vec3().copy(el.getAttribute('position'))
      // );
    },100)

    setInterval(function () {
      var score = 0
      AFRAME.scenes[0].querySelectorAll('.pins').forEach(function (p) {
        score += p.object3D.userData.tipped ? 1 : 0
        // console.log(p.object3D.userData.tipped)
      })
      console.log(score)
    },1000)
  }
}
</script>

<style>
</style>
