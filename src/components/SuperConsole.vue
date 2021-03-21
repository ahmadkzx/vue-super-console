<template>
	<div class="super-console">
		<div class="super-console-header">
			<span class="super-console__title">SUPER CONSOLE</span>
			<button class="super-console__close"></button>
		</div>

		<div class="super-console-logs" v-if="logs.length">
			<SuperLog v-for="(log, index) in logs" :key="index" :logTitle="log.title" :logContent="log.content" :logType="log.type" />
		</div>

		<div class="super-console__empty" v-else>Console Is Empty</div>

		<div class="super-console-footer">

			<div
				:key="index"
				:class="['super-console-footer-log', logType]"
				v-for="(logType, index) in ['info', 'error', 'warning', 'success']"
			>
				<img class="super-console-footer-log__icon" :src="require(`@/assets/icons/${logType}.svg`)">
				<span class="super-console-footer-log__count">{{ logsCount[logType] || 0 }}</span>
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

	computed: {
		logsCount() {
			let logsCount = {}
			this.logs.map(log => (logsCount[log.type]) ? logsCount[log.type]++ : logsCount[log.type] = 1)
			return logsCount
		}
	},

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
			return function () {
				this.addLog(type, arguments)
				return method.apply(console, arguments)
			}.bind(this)
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
	},
}
</script>

<style src="@/assets/sass/superConsole.scss" lang="scss" scoped />