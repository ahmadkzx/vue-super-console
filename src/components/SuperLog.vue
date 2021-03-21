<template>
	<div :class="['log', logType, { 'remove': isRemoving }]">
		<div class="log-icon">
			<img :src="require(`@/assets/icons/${logType}.svg`)">
		</div>
		<div class="log-section">
			<div class="log-section__content">
				{{ logContent }}
			</div>
			<div class="log-section-footer">
				<div class="log-section-footer-infos">
					<div class="log-section-footer-info">
						<span class="log-section-footer-info__key">Log Time:</span>
						<span class="log-section-footer-info__data">23:15:20</span>
					</div>

					<div class="log-section-footer-info">
						<span class="log-section-footer-info__key">Type:</span>
						<span class="log-section-footer-info__data">String</span>
					</div>
				</div>

				<button class="log-section-footer__removeBtn" @click="removeLog(logId)"></button>
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
		isRemoving: false
	}),

	methods: {
		removeLog(logId) {
			if (this.logId != logId) return
			this.isRemoving = true
			setTimeout(() => this.$emit('removeLog', this.logId), 300)
		}
	}
}
</script>

<style src="@/assets/sass/superLog.scss" lang="scss" scoped />