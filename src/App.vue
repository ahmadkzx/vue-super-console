<template>
  <div id="app">
		<img src="@/assets/test.jpg" style="position: absolute">
    <SuperConsoleFab />
  </div>
</template>

<script>
import SuperConsoleFab from './components/SuperConsoleFab'

export default {
  name: 'App',
  components: {
    SuperConsoleFab
  }
}
</script>