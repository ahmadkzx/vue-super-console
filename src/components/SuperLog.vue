<template>
	<div :class="['log', logType, { 'remove': isRemoving }]">
		<div class="log-icon">
			<i :class="`si si-${logType}`"></i>
		</div>
		<div class="log-section">
			<div class="log-section__content" v-html="logContent"></div>
			<div class="log-section-footer">
				<div class="log-section-footer-infos">
					<div class="log-section-footer-infos-item">
						<span class="log-section-footer-infos-item__key">Log Time:</span>
						<span class="log-section-footer-infos-item__data">{{ logTime }}</span>
					</div>

					<div class="log-section-footer-infos-item">
						<span class="log-section-footer-infos-item__key">Type:</span>
						<span class="log-section-footer-infos-item__data">{{ logContentType }}</span>
					</div>
				</div>

				<div>
					<button class="log-section-footer__btn delete" @click="googleError" v-if="logType == 'error'">
						<i class="si si-google"></i>
					</button>
					<button class="log-section-footer__btn delete" @click="removeLog(logId)">
						<i class="si si-trash"></i>
					</button>
					<button class="log-section-footer__btn delete" @click="takeScreenshot">
						<i class="si si-screenshot"></i>
					</button>
					<button :class="['log-section-footer__btn copy', { 'copied': isCopied }]" @click="copy">
						<i class="si si-copy" v-if="!isCopied"></i>
						<i class="si si-success" v-else></i>
					</button>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import domtoimage from 'dom-to-image'

export default {
	name: 'SuperLog',

	props: {
		logContent: {
			required: true
		},
		logType: {
			type: String,
			required: true
		},
		logId: {
			type: Number,
			required: true
		},
		logContentType: {
			type: String,
			required: true
		},
		logPureContent: {
			type: String,
			required: true
		}
	},

	data: () => ({
		isCopied: false,
		isRemoving: false
	}),

	computed: {
		logTime() {
			return new Date().toLocaleTimeString()
		}
	},

	methods: {
		removeLog(logId) {
			if (this.logId != logId) return
			this.isRemoving = true
			setTimeout(() => this.$emit('removeLog', this.logId), 300)
		},

		copy() {
			if (this.isCopied) return
			let inp = document.createElement('input')
			inp.value = this.logContent
			document.body.appendChild(inp)
			inp.select()
			document.execCommand('copy')
			document.body.removeChild(inp)

			this.isCopied = true
			setTimeout(() => this.isCopied = false, 1000)
		},

		googleError() {
			const searchQuery = encodeURIComponent(`how to fix ${this.logPureContent}`)
			if (process.browser) window.open(`https://google.com/search?q=${searchQuery}`, '_blank')
		},

		takeScreenshot() {
			domtoimage.toJpeg(this.$el)
				.then(dataUrl => {
					let link = document.createElement('a')
					link.download = 'log.jpg'
					link.href = dataUrl
					link.click()
				})
		}
	}
}
</script>

<style src="@/assets/sass/superLog.scss" lang="scss" scoped />