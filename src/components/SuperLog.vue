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
						<span class="log-section-footer-infos-item__data">23:15:20</span>
					</div>

					<div class="log-section-footer-infos-item">
						<span class="log-section-footer-infos-item__key">Type:</span>
						<span class="log-section-footer-infos-item__data">String</span>
					</div>
				</div>

				<div>
					<button class="log-section-footer__btn delete" @click="removeLog(logId)">
						<i class="si si-trash"></i>
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
export default {
	name: 'SuperLog',

	props: {
		logTitle: {
			required: false
		},
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
		}
	},

	data: () => ({
		isCopied: false,
		isRemoving: false
	}),

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
		}
	}
}
</script>

<style src="@/assets/sass/superLog.scss" lang="scss" scoped />