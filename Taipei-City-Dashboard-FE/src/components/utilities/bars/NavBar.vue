<!-- Developed by Taipei Urban Intelligence Center 2023-2024-->

<!-- Navigation will be hidden from the navbar in mobile mode and moved to the settingsbar -->

<script setup>
const { VITE_APP_TITLE } = import.meta.env;
import { computed } from "vue";
import { useRoute } from "vue-router";
import { useFullscreen } from "@vueuse/core";
import { useAuthStore } from "../../../store/authStore";
import { useDialogStore } from "../../../store/dialogStore";

import UserSettings from "../../dialogs/UserSettings.vue";
import ContributorsList from "../../dialogs/ContributorsList.vue";
import Image1 from "../../../assets/images/Img1.gif";

const route = useRoute();
const authStore = useAuthStore();
const dialogStore = useDialogStore();
const { isFullscreen, toggle } = useFullscreen();

const linkQuery = computed(() => {
	const { query } = route;
	return `?index=${query.index}`;
});

import { ref, onMounted, onUnmounted } from "vue";
const animalCanvas = ref(null);
let animationFrameId = null;
let animalImage = new Image();

animalImage.src = Image1;

const animal = {
	x: 0, // 圈圈起始位置
	y: 0, // 圈圈起始位置
	width: 60,
	height: 60,
	speed: 1, // 調整速度
};
function drawAnimal(ctx) {
	// // 畫圈圈
	// ctx.beginPath();
	// ctx.arc(animal.x, animal.y, animal.size, 0, Math.PI * 2);
	// ctx.fillStyle = "orange";
	// ctx.fill();
	ctx.drawImage(animalImage, animal.x, animal.y, animal.width, animal.height);
}
function updateAnimal(canvasWidth) {
	// 動態調整圈圈位置
	animal.x += animal.speed;
	if (animal.x > canvasWidth) {
		animal.x = -animal.width;
	}
}
function animate() {
	const canvas = animalCanvas.value;
	const ctx = canvas.getContext("2d");
	ctx.clearRect(0, 0, canvas.width, canvas.height);
	drawAnimal(ctx);
	updateAnimal(canvas.width);
	animationFrameId = requestAnimationFrame(animate);
}
onMounted(() => {
	const canvas = animalCanvas.value;
	canvas.width = window.innerWidth; // 使canvas寬度與窗口寬度相同
	canvas.height = 60; // 設置固定高度
	animalImage.onload = () => {
		animate();
	};
	window.addEventListener("resize", () => {
		canvas.width = window.innerWidth;
	});
});
onUnmounted(() => {
	if (animationFrameId) {
		cancelAnimationFrame(animationFrameId);
	}
	window.removeEventListener("resize", () => {});
});
</script>

<template>
	<div>
		<div class="navbar">
		<a href="/">
			<div class="navbar-logo">
				<div class="navbar-logo-image">
					<img
						src="../../../assets/images/TUIC.svg"
						alt="tuic logo"
					/>
				</div>
				<div>
					<h1>{{ VITE_APP_TITLE }}</h1>
					<h2>Taipei City Dashboard</h2>
				</div>
			</div>
		</a>
		<div
			v-if="
				authStore.currentPath !== 'admin' &&
				!(authStore.isMobileDevice && authStore.isNarrowDevice)
			"
			class="navbar-tabs"
		>
			<router-link
				v-if="authStore.token"
				:to="`/component`"
				:class="{
					'router-link-active':
						authStore.currentPath.includes('component'),
				}"
			>
				組件瀏覽平台
			</router-link>
			<router-link
				:to="`/dashboard${
					linkQuery.includes('undefined') ? '' : linkQuery
				}`"
			>
				儀表板總覽
			</router-link>
			<router-link
				:to="`/mapview${
					linkQuery.includes('undefined') ? '' : linkQuery
				}`"
			>
				地圖交叉比對
			</router-link>
		</div>
		<div class="navbar-user">
			<button
				v-if="
					!(authStore.isMobileDevice && authStore.isNarrowDevice)
				"
				class="hide-if-mobile"
				@click="toggle"
			>
				<span>{{
					isFullscreen ? "fullscreen_exit" : "fullscreen"
				}}</span>
			</button>
			<button v-if="authStore.token" @click="authStore.toggleMode">
				<span>{{
					authStore.user.mode === "light" ? "dark_mode" : "light_mode"
				}}</span>
			</button>
			<div class="navbar-user-info">
				<button><span>info</span></button>
				<ul>
					<li>
						<a
							href="https://tuic.gov.taipei/documentation"
							target="_blank"
							rel="noreferrer"
							>技術文件</a
						>
					</li>
					<li>
						<button
							@click="dialogStore.showDialog('contributorsList')"
						>
							專案貢獻者
						</button>
					</li>
				</ul>
				<teleport to="body">
					<ContributorsList />
				</teleport>
			</div>
			<div
				v-if="
					authStore.token &&
					!(authStore.isMobileDevice && authStore.isNarrowDevice)
				"
				class="navbar-user-user"
			>
				<button>
					{{ authStore.user.name }}
				</button>
				<ul>
					<li>
						<button @click="dialogStore.showDialog('userSettings')">
							用戶設定
						</button>
					</li>
					<li
						v-if="
							authStore.currentPath !== 'admin' &&
							authStore.user.is_admin
						"
						class="hide-if-mobile"
					>
						<router-link to="/admin"> 管理員後臺 </router-link>
					</li>
					<li
						v-else-if="authStore.user.is_admin"
						class="hide-if-mobile"
					>
						<router-link to="/dashboard"> 返回儀表板 </router-link>
					</li>
					<li>
						<button @click="authStore.handleLogout">登出</button>
					</li>
				</ul>
				<teleport to="body">
					<user-settings />
				</teleport>
			</div>
			<div
				v-else-if="
					!(authStore.isMobileDevice && authStore.isNarrowDevice)
				"
				class="navbar-user-user"
			>
				<button @click="dialogStore.showDialog('login')">登入</button>
			</div>
		</div>
		<canvas
			ref="animalCanvas"
		></canvas>
	</div>

	</div>
	
</template>

<style scoped lang="scss">
canvas {
	position: relative; //絕對位置:自己獨立一層
	// width: 100%;
	height: 60px;
	//border-bottom: 1px solid var(--color-border);
	pointer-events: none; // 使canvas不影響鼠標事件
	z-index: 1; // 圈圈在下
}

.navbar {
	position: absolute; //絕對位置
	height: 60px;
	width: 100vw;
	display: flex;
	justify-content: space-between;
	align-items: center;
	border-bottom: 1px solid var(--color-border);
	background-color: var(--color-component-background);
	user-select: none;
	z-index: 1; // 確保canvas在其他元素下方

	&-logo {
		display: flex;

		h1 {
			font-weight: 500;
		}

		h2 {
			font-size: var(--font-s);
			font-weight: 400;
		}

		&-image {
			width: 22.94px;
			height: 45px;
			margin: 0 var(--font-m);

			img {
				height: 45px;
				filter: var(--img-filter);
			}
		}
	}

	&-tabs {
		display: flex;

		a {
			height: 59px;
			display: flex;
			align-items: center;
			margin-left: var(--font-s);
			transition: opacity 0.2s, border-bottom 0.2s;
			border-bottom: solid 3px transparent;

			&:hover {
				opacity: 0.8;
			}
		}

		.router-link-active {
			border-bottom: solid 3px var(--color-highlight);
			color: var(--color-highlight);

			&:hover {
				opacity: 1;
			}
		}

		@media screen and (max-width: 750px) {
			display: none;
		}
		@media screen and (max-height: 500px) {
			display: none;
		}
	}

	&-user {
		display: flex;
		align-items: center;

		li a,
		button {
			display: flex;
			align-items: center;
			margin-right: var(--font-m);
			padding: 2px 4px;
			border-radius: 1px;
			font-size: var(--font-m);
			transition: background-color 0.25s;
		}

		span {
			font-family: var(--font-icon);
			font-size: calc(var(--font-l) * var(--font-to-icon));
		}

		&-user:hover ul,
		&-info:hover ul {
			display: block;
			opacity: 1;
		}

		&-user,
		&-info {
			height: 60px;
			min-width: 100px;
			display: flex;
			align-items: center;
			justify-content: center;

			@media screen and (max-width: 750px) {
				display: none;
			}
			@media screen and (max-height: 500px) {
				display: none;
			}

			ul {
				min-width: 100px;
				display: none;
				position: absolute;
				right: 20px;
				top: 55px;
				padding: 8px;
				border-radius: 5px;
				background-color: var(--color-component-background);
				border: 1px solid var(--color-border);
				opacity: 0;
				transition: opacity 0.25s;
				z-index: 10;

				li {
					border-radius: 5px;
					transition: background-color 0.25s;

					a,
					button {
						padding: 8px 6px;
						width: 100%;
						height: 100%;
					}
				}

				li:hover {
					background-color: var(--color-complement-text);
				}
			}
		}

		&-info {
			min-width: 0;

			ul {
				right: 120px;
				top: 55px;
			}

			@media screen and (max-width: 750px) {
				display: flex;

				ul {
					right: 20px;
					top: 55px;
				}
			}
			@media screen and (max-height: 500px) {
				display: flex;
			}
		}
	}
}
</style>
