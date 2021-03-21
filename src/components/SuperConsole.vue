<template>
	<div class="super-console">
		<div class="super-console-header">
			<span class="super-console__title">SUPER CONSOLE</span>
			<button class="super-console__close"></button>
		</div>

		<div class="super-console-logs">
			<SuperLog v-for="(log, index) in logs" :key="index" :logTitle="log.title" :logContent="log.content" :logType="log.type" />
		</div>

		<div class="super-console-footer">
			<div class="super-console-footer-log info">
				<img class="super-console-footer-log__icon" src="@/assets/icons/info.svg">
				<span class="super-console-footer-log__count">25</span>
			</div>
			<div class="super-console-footer-log error">
				<img class="super-console-footer-log__icon" src="@/assets/icons/error.svg">
				<span class="super-console-footer-log__count">25</span>
			</div>
			<div class="super-console-footer-log success">
				<img class="super-console-footer-log__icon" src="@/assets/icons/success.svg">
				<span class="super-console-footer-log__count">25</span>
			</div>
			<div class="super-console-footer-log warning">
				<img class="super-console-footer-log__icon" src="@/assets/icons/warning.svg">
				<span class="super-console-footer-log__count">25</span>
			</div>
		</div>
	</div>
</template>

<script>
import SuperLog from './SuperLog'

export default {
	name: 'SuperConsole',

	components: {
		SuperLog
	},

	data: () => ({
		logs: []
	}),

	mounted() {
		this.setConsoleListeners()
	},

	methods: {
		setConsoleListeners() {
			let cLog = console.log
			let cWarn = console.warn
			let cError = console.error
			
			console.log = this.proxy(cLog, 'info')
			console.warn = this.proxy(cWarn, 'warning')
			console.error = this.proxy(cError, 'error')
			console.success = this.proxy(cLog, 'success')

		},

		proxy(method, type) {
			let res = this
			return function () {
				res.addLog(type, arguments)
				return method.apply(console, arguments)
			}
		},

		addLog(type, args) {
			let payload = { type }
			if (args.length > 1) {
				payload.title = args[0]
				payload.content = args[1]
			} else {
				payload.content = args[0]
			}
			this.logs.push(payload)
		}
	}
}
</script>

<style src="@/assets/sass/superConsole.scss" lang="scss" scoped />