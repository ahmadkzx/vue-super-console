<template>
	<div :class="['log', logType]">
		<div class="log-icon">
			<img :src="require(`@/assets/icons/${logType}.svg`)">
		</div>
		<div class="log-section">
			<div class="log-section__content">
				page editors now use Lorem Ipsum as their default model text, and a search for 'lorem ipsum' will uncover many web sites still in their infancy
			</div>
			<div class="log-section-footer">
				<div class="log-section-footer-info">
					<span class="log-section-footer-info__key">Log Time:</span>
					<span class="log-section-footer-info__data">23:15:20</span>
				</div>

				<div class="log-section-footer-info">
					<span class="log-section-footer-info__key">Type:</span>
					<span class="log-section-footer-info__data">String</span>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: 'SuperLog',

	props: {
		log: {
			required: true
		},
		logType: {
			type: String,
			required: true
		}
	}
}
</script>

<style src="@/assets/sass/superLog.scss" lang="scss" scoped />