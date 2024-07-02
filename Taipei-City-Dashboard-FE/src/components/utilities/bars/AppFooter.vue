<template>
	<footer class="app-footer">
		<div class="footer-content">
			<div class="footer-logo">
				<img src="/src/assets/images/TUIC.svg" alt="TUIC Logo" />
			</div>
			<div class="footer-info">
				<p>Developed By Taipei Urban Intelligence Center 2023-2024</p>
				<p>Department of Information Technology, Taipei City Government</p>
			</div>
		</div>
	</footer>
</template>

<style>
.app-footer {
	border-top: 1px solid var(--color-border);
	display: inline-block;
	height: 60px;
	background-color: var(--color-component-background);
	color: var(--color-complement-text);
	padding: 3px;
	font-size: 0.8rem;

	.footer-content {
		padding: 10px 0;
		max-width: 1200px;
		margin: 0 auto;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.footer-logo {
		padding: 0 15px;
		flex: 1;
		img {
			filter: var(--img-filter);
			height: 2.5rem;
		}
	}

	.footer-info {
		padding: 0 20px;
		flex: 10;
		text-align: right; /* 確保右側信息對齊 */
	}

	p {
		margin: 5px 0;
		line-height: 15px; /* 字體間距 */
	}
}
</style>
