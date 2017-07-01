<template>
  <div id="app">
    <a-scene physics="debug: false">
      <a-assets>
      </a-assets>
      <!-- Floor -->
      <a-plane static-body position='0 -2 0' height='150' width='150' color='#a3b3c9' rotation='-90 0 0'></a-plane>
      <!-- Dynamic box -->
      <!-- <a-box dynamic-body position="0 0 -10" width="1" height="1" depth="1"></a-box>
      <a-box dynamic-body position="0 0 -11" width="1" height="2" depth="1"></a-box> -->
      <a-box dynamic-body position="2 1 -18" width="1" height="1" depth="1" color='#16F'></a-box>
      <a-box dynamic-body position="1 1 -18" width="1" height="1" depth="1" color='#26F'></a-box>
      <a-box dynamic-body position="0 1 -18" width="1" height="1" depth="1" color='#36F'></a-box>
      <a-box dynamic-body position="-1 1 -18" width="1" height="1" depth="1" color='#46F'></a-box>

      <a-box dynamic-body position="2 1 -20" width="1" height="2.5" depth="1" color='#36F'></a-box>
      <a-box dynamic-body position="1 1 -20" width="1" height="2.5" depth="1" color='#46F'></a-box>
      <a-box dynamic-body position="0 1 -20" width="1" height="2.5" depth="1" color='#56F'></a-box>
      <a-box dynamic-body position="-1 1 -20" width="1" height="2.5" depth="1" color='#66F'></a-box>

      <a-box dynamic-body position="2 2 -23" width="1" height="4" depth="1" color='#34F'></a-box>
      <a-box dynamic-body position="1 2 -23" width="1" height="4" depth="1" color='#35F'></a-box>
      <a-box dynamic-body position="0 2 -23" width="1" height="4" depth="1" color='#37F'></a-box>
      <a-box dynamic-body position="-1 2 -23" width="1" height="4" depth="1" color='#31F'></a-box>

      <a-sphere id='ball' dynamic-body='mass: 15;' position="0.5 0 10" radius='1'>        
      </a-sphere>
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
  },
  mounted () {
    var self = this
    setTimeout(function(){
      var el = AFRAME.scenes[0].querySelector('#ball');
      // el.body.velocity.z -= 50.1
      el.body.applyImpulse(
        /* impulse */        new CANNON.Vec3(0, 0, -500),
        /* world position */ new CANNON.Vec3().copy(el.getAttribute('position'))
      );
    },100)
  }
}
</script>

<style>
</style>
