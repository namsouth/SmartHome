<template lang="pug">
.shower
	.cloud
	.sun: .rays
	.rain
</template>
<style lang="stylus">
.shower
	position absolute
	top 50%
	left 88%
	font-size 1em
	color #f6f6f6

.sun
	width 2.5em
	height 2.5em
	margin -1.25em
	background currentColor
	border-radius 50%
	box-shadow 0 0 0 0.375em orange
	animation spin 12s infinite linear

.cloud + .sun
	margin: -2em 1em

.rays
	position absolute
	top -2em
	left 50%
	display block
	width 0.375em
	height 1.125em
	margin-left -0.1875em
	background yellow
	border-radius 0.25em
	box-shadow 0 5.375em yellow
	&:before,&:after
		content  ''
		position  absolute
		top  0em
		left  0em
		display  block
		width  0.375em
		height  1.125em
		transform  rotate(60deg)
		transform-origin  50% 3.25em
		background  yellow
		border-radius  0.25em
		box-shadow 0 5.375em yellow
	&:before
		transform rotate(120deg)

.rain
	position absolute
	z-index 2
	top 50%
	left 20%
	width 3.75em
	height 3.75em
	margin 0.375em 0 0 -2em
	background currentColor
	&:after
		content ''
		position absolute
		z-index 2
		top 50%
		left 50%
		width 1.125em
		height 1.125em
		margin -1em 0 0 -0.25em
		background #0cf
		border-radius 100% 0 60% 50% / 60% 0 100% 50%
		box-shadow 0.625em 0.875em 0 -0.125em rgba(255, 255, 255, 0.2), -0.875em 1.125em 0 -0.125em rgba(255, 255, 255, 0.2), -1.375em -0.125em 0 rgba(255, 255, 255, 0.2)
		transform rotate(-28deg)
		animation rain 3s linear infinite

.cloud
	position absolute
	z-index 1
	width 3.6875em
	height 3.6875em
	margin -1.84375em
	background currentColor
	border-radius 50%
	box-shadow -2.1875em 0.6875em 0 -0.6875em, 2.0625em 0.9375em 0 -0.9375em, 0 0 0 0.375em lightgray, -2.1875em 0.6875em 0 -0.3125em lightgray, 2.0625em 0.9375em 0 -0.5625em lightgray
	&:after
		content ''
		position absolute
		bottom 0
		left -0.5em
		display block
		width 4.5625em
		height 1em
		background currentColor
		box-shadow 0 0.4375em 0 -0.0625em lightgray

@keyframes rain
	0%
		background #0cf
		box-shadow 0.625em 0.875em 0 -0.125em rgba(255, 255, 255, 0.2), -0.875em 1.125em 0 -0.125em rgba(255, 255, 255, 0.2), -1.375em -0.125em 0 #0cf
	25%
		box-shadow 0.625em 0.875em 0 -0.125em rgba(255, 255, 255, 0.2), -0.875em 1.125em 0 -0.125em #0cf, -1.375em -0.125em 0 rgba(255, 255, 255, 0.2)
	50%
		background rgba(255, 255, 255, 0.3)
		box-shadow 0.625em 0.875em 0 -0.125em #0cf, -0.875em 1.125em 0 -0.125em rgba(255, 255, 255, 0.2), -1.375em -0.125em 0 rgba(255, 255, 255, 0.2)
	100%
		box-shadow 0.625em 0.875em 0 -0.125em rgba(255, 255, 255, 0.2), -0.875em 1.125em 0 -0.125em rgba(255, 255, 255, 0.2), -1.375em -0.125em 0 #0cf

@keyframes spin
	to
		transform rotate(360deg)
</style>
