<template>
	<div class="super-console">
		<div class="super-console-header">
			<span class="super-console__title">SUPER CONSOLE</span>
			<button class="super-console__clear" @click="clearLogs">
				<i class="si si-trash"></i>
			</button>
		</div>

		<div class="super-console-search">
			<input type="text" class="super-console-search__input" v-model="searchQuery" placeholder="Search Text...">
			<button class="super-console-search__btn" @click="clearLogs">
				<i class="si si-search"></i>
			</button>
		</div>

		<div class="pad" v-if="filteredLogs.length">
			<div class="super-console-logs">
				<SuperLog
					v-for="log in filteredLogs"
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
				@click="filterLogsByType(logType)"
				v-for="(logType, index) in ['info', 'error', 'warning', 'success']"
				:class="['super-console-footer-log', logType, { 'off': selectedErrorType && selectedErrorType != logType }]"
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
		logsPayload: [],
		searchQuery: '',
		selectedErrorType: ''
	}),

	computed: {
		logsCount() {
			let logsCount = {}
			this.logsPayload.map(log => (logsCount[log.type]) ? logsCount[log.type]++ : logsCount[log.type] = 1)
			return logsCount
		},

		filteredLogs() {
			let filteredLogs = this.logsPayload

			if (this.selectedErrorType) filteredLogs = filteredLogs.filter(log => log.type == this.selectedErrorType)
			if (this.searchQuery) filteredLogs = filteredLogs.filter(log => log.content.indexOf(this.searchQuery) >= 0)

			return filteredLogs
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
				id: this.logsPayload[this.logsPayload.length - 1]?.id + 1 || 1
			}
			this.logsPayload.push(payload)
			this.scrollToBottom()
		},

		removeLog(id) {
			this.logsPayload = this.logsPayload.filter(log => log.id != id)
		},

		clearLogs() {
			this.logsPayload = []
		},

		scrollToBottom() {
			setTimeout(() => {
				document.getElementsByClassName('super-console-logs')[0].scrollTop
				= document.getElementsByClassName('super-console-logs')[0].scrollHeight
			}, 0)
		},

		filterLogsByType(type) {
			this.selectedErrorType = (this.selectedErrorType == type) ? '' : type
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