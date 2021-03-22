<template>
	<div class="super-console">
		<div class="super-console-header">
			<span class="super-console__title">SUPER CONSOLE</span>
			<button class="super-console__close">
				<i class="si si-arrow"></i>
			</button>
		</div>

		<div class="pad" v-if="logs.length">
			<div class="super-console-logs">
				<SuperLog
					v-for="log in logs"
					:key="log.id"
					:logId="log.id"
					:logType="log.type"
					:logTitle="log.title"
					@removeLog="removeLog"
					:logContent="log.content"
				/>
			</div>
		</div>

		<div class="super-console__empty" v-else>Console Is Empty</div>

		<div class="super-console-footer">

			<div
				:key="index"
				:class="['super-console-footer-log', logType]"
				v-for="(logType, index) in ['info', 'error', 'warning', 'success']"
			>
				<i :class="`si si-${logType} super-console-footer-log__icon`"></i>
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
			let payload = {
				type,
				content: this.getLogContent(args),
				id: this.logs[this.logs.length - 1]?.id + 1 || 1
			}
			this.logs.push(payload)
			this.scrollToBottom()
		},

		scrollToBottom() {
			setTimeout(() => {
				document.getElementsByClassName('super-console-logs')[0].scrollTop
				= document.getElementsByClassName('super-console-logs')[0].scrollHeight
			}, 0)
		},

		removeLog(id) {
			this.logs = this.logs.filter(log => log.id != id)
		},

		getLogContent(args) {
			let newArgs = []
			let isStyled = false
			Array.prototype.map.call(args, arg => {
				if (isStyled && this.isCss(arg)) return
				if (arg.indexOf('%c') >= 0) {
					isStyled = true;
					arg = arg.replaceAll('%c', '')
				}
				newArgs.push(arg)
			})
			return newArgs.join(' ')
		},

		isCss(str) {
			let doc = document.implementation.createHTMLDocument('')
			let styleEl = document.createElement('style')
			doc.body.appendChild(styleEl);
			styleEl.textContent = `* {${ str }}`
			return !!styleEl.sheet.cssRules[0].styleMap.size;
		},
	},
}
</script>

<style src="@/assets/sass/superConsole.scss" lang="scss" scoped />