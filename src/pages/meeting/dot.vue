<template>
	<div class="dot">
		<a
			:href="'https://meet.jit.si/' + meeting"
			target="meeting_iframe"
			onclick="breakout(this)"
		>
			<img type="image/svg+xml" src=src />
		</a>
	</div>
</template>
<script>


export default {
	props : ['src', 'meeting']
}
</script>
