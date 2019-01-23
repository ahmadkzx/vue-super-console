<template>
	<div class="super-console">
		<div class="super-console-header">
			<span class="super-console__title">SUPER CONSOLE</span>
			<button class="super-console__close"></button>
		</div>

		<div class="super-console-logs">
			<SuperLog log="salam" logType="error" />
			<SuperLog log="salam" logType="info" />
			<SuperLog log="salam" logType="success" />
			<SuperLog log="salam" logType="warning" />
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
	}
}
</script>

<style src="@/assets/sass/superConsole.scss" lang="scss" scoped />