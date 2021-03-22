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
					@removeLog="removeLog"
					:logContent="log.content"
					:logContentType="log.contentType"
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

		addLog(type, args) {
			const { logContent, logContentType } = this.getLogContentAndContentType(args)
			const payload = {
				type,
				content: logContent,
				contentType: logContentType,
				id: this.logs[this.logs.length - 1]?.id + 1 || 1
			}
			this.logs.push(payload)
			this.scrollToBottom()
		},

		removeLog(id) {
			this.logs = this.logs.filter(log => log.id != id)
		},

		scrollToBottom() {
			setTimeout(() => {
				document.getElementsByClassName('super-console-logs')[0].scrollTop
				= document.getElementsByClassName('super-console-logs')[0].scrollHeight
			}, 0)
		},

		proxy(method, type) {
			return function () {
				this.addLog(type, arguments)
				return method.apply(console, arguments)
			}.bind(this)
		},

		getLogContentAndContentType(args) {
			let newArgs = []
			let isStyled = false
			let logContentType = ''
			Array.prototype.map.call(args, arg => {

				// DETECT LOG CONTENT TYPE
				logContentType = (!!logContentType && typeof arg != logContentType) ? 'multi type' : typeof arg

				
				// BEAUTIFY OBJECT
				if (typeof arg == 'object') {
					arg = `<pre>${JSON.stringify(arg, null, 2)}</pre>`
				}

				// REMOVE CSS
				if (isStyled && this.isCss(arg)) return

				// REMOVE %c (this is using for styling log)
				if (typeof arg == 'string' && arg.indexOf('%c') >= 0) {
					isStyled = true;
					arg = arg.replaceAll('%c', '')
				}

				newArgs.push( this.highlightSyntax(arg) )

			})
			return {
				logContentType,
				logContent: newArgs.join(' ')
			}
		},

		isCss(str) {
			let doc = document.implementation.createHTMLDocument('')
			let styleEl = document.createElement('style')
			doc.body.appendChild(styleEl);
			styleEl.textContent = `* {${ str }}`
			return !!styleEl.sheet.cssRules[0].styleMap.size;
		},

		highlightSyntax(txt) {
			if (txt === true) return '<span style="color: #00ff6c">true</span>'
			if (txt === false) return '<span style="color: #ff0042">false</span>'
			if (typeof txt != 'string') return txt
			txt = txt.replaceAll(':', '<span style="color: aqua">:</span>')
			txt = txt.replaceAll('true', '<span style="color: #00ff6c">true</span>')
			txt = txt.replaceAll('false', '<span style="color: #ff0042">false</span>')
			txt = txt.replaceAll('{', '<span style="color: aqua">{</span>')
			txt = txt.replaceAll('}', '<span style="color: aqua">}</span>')
			txt = txt.replaceAll('<pre', '<pre style="font-size: 1rem; margin: 0 !important;"')
			return txt
		}
	},
}
</script>

<style src="@/assets/sass/superConsole.scss" lang="scss" scoped />