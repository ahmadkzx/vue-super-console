<template>
  <div id="app">
		<img src="@/assets/test.jpg" style="position: absolute">
    <SuperConsole />
  </div>
</template>

<script>
import SuperConsole from './components/SuperConsole'

export default {
  name: 'App',
  components: {
    SuperConsole
  }
}
</script>
