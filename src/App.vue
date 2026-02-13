<script setup>
import { ref, computed } from 'vue';

const screen = ref('question');
const dodgeCount = ref(0);
const noButtonPos = ref({ x: 0, y: 0 });
const noButtonFloating = ref(false);
const buttonsArea = ref(null);

const yesButtonExcited = computed(() => dodgeCount.value >= 3);

function onNoHover(event) {
	dodgeCount.value++;

	const container = buttonsArea.value;
	if (!container) return;
	const containerRect = container.getBoundingClientRect();

	const btn = event.target;
	const btnRect = btn.getBoundingClientRect();
	const btnWidth = btnRect.width;
	const btnHeight = btnRect.height;

	if (!noButtonFloating.value) {
		noButtonFloating.value = true;
	}

	const padding = 10;
	const maxX = containerRect.width - btnWidth - padding;
	const maxY = containerRect.height - btnHeight - padding;

	const mouseX = event.clientX - containerRect.left;
	const mouseY = event.clientY - containerRect.top;
	const minDistance = 100;
	let newX, newY;
	let attempts = 0;

	do {
		newX = padding + Math.random() * Math.max(0, maxX - padding);
		newY = padding + Math.random() * Math.max(0, maxY - padding);
		attempts++;
		const dist = Math.sqrt(
			(newX - mouseX) ** 2 + (newY - mouseY) ** 2,
		);
		if (dist >= minDistance || attempts > 10) break;
	} while (true);

	noButtonPos.value = { x: newX, y: newY };
}

function onYes() {
	screen.value = 'celebration';
}
</script>

<template>
	<!-- Question Screen -->
	<div v-if="screen === 'question'" class="card">
		<div class="icon">😻</div>
		<h1>Abby will you be my valentine?</h1>

		<div ref="buttonsArea" class="buttons-area">
			<button
				class="yes-btn"
				:class="{ excited: yesButtonExcited }"
				@click="onYes"
			>
				Yes
			</button>
			<button
				class="no-btn"
				:style="
					noButtonFloating
						? {
								position: 'absolute',
								left:
									noButtonPos.x +
									'px',
								top:
									noButtonPos.y +
									'px',
							}
						: {}
				"
				@mouseenter="onNoHover"
			>
				No
			</button>
		</div>

		<p v-if="yesButtonExcited" class="hint-text">
			hmmmm something must be wrong with no
		</p>
	</div>

	<!-- Celebration Screen -->
	<div v-else class="card celebration">
		<div class="celebration-icon">🎉</div>
		<h1>Great Choice! Woohoo!</h1>
		<p class="celebration-subtitle">
			Happy Valentine's Day Baby ❤️
		</p>
		<img
			class="celebration-img"
			src="https://di2ponv0v5otw.cloudfront.net/posts/2023/08/13/64d998d42fbf1aa82eee1b49/m_wp_64d998d42fbf1aa82eee1b4a.webp"
			alt="Dachshund with chocolates"
		/>
	</div>
</template>

<style scoped>
.card {
	background: white;
	border-radius: 24px;
	padding: 1rem;
	box-shadow: 0 8px 32px rgba(200, 100, 150, 0.2);
	text-align: center;
	max-width: 600px;
	width: 90vw;
	max-height: 95vh;
	display: flex;
	flex-direction: column;
	overflow: hidden;
}

.icon {
	font-size: 5rem;
	margin-bottom: 1rem;
}

h1 {
	font-size: 1.8rem;
	margin-bottom: 2rem;
	color: #d63384;
	font-weight: 700;
}

/* Buttons area */
.buttons-area {
	position: relative;
	min-height: 150px;
	display: flex;
	justify-content: center;
	align-items: flex-start;
	gap: 1.5rem;
	padding: 1rem;
}

.yes-btn {
	padding: 0.8rem 2rem;
	font-size: 1.2rem;
	background-color: #28a745;
	color: white;
	border: none;
	border-radius: 12px;
	cursor: pointer;
	transition: all 0.3s ease;
	position: relative;
	z-index: 1;
}

.yes-btn:hover {
	background-color: #218838;
	transform: scale(1.05);
}

.yes-btn.excited {
	font-size: 1.8rem;
	padding: 1.2rem 3rem;
	background-color: #ff1493;
	animation: pulse 0.8s ease-in-out infinite;
	box-shadow: 0 0 20px rgba(255, 20, 147, 0.5);
}

.yes-btn.excited:hover {
	background-color: #e0117f;
}

@keyframes pulse {
	0%,
	100% {
		transform: scale(1);
	}
	50% {
		transform: scale(1.1);
	}
}

.no-btn {
	padding: 0.8rem 2rem;
	font-size: 1.2rem;
	background-color: #dc3545;
	color: white;
	border: none;
	border-radius: 12px;
	cursor: pointer;
	transition:
		left 0.15s ease-out,
		top 0.15s ease-out;
	user-select: none;
}

.hint-text {
	margin-top: 1rem;
	font-style: italic;
	color: #888;
	font-size: 0.95rem;
	animation: fadeIn 0.5s ease-in;
}

@keyframes fadeIn {
	from {
		opacity: 0;
		transform: translateY(10px);
	}
	to {
		opacity: 1;
		transform: translateY(0);
	}
}

/* Celebration screen */
.celebration {
	background: linear-gradient(135deg, #fff0f5, #ffe4ec);
}

.celebration-icon {
	font-size: 4rem;
	animation: bounce 0.6s ease-in-out infinite alternate;
}

@keyframes bounce {
	from {
		transform: translateY(0);
	}
	to {
		transform: translateY(-15px);
	}
}

.celebration h1 {
	color: #ff1493;
	font-size: 2.5rem;
}

.celebration-subtitle {
	font-size: 1.3rem;
	color: #d63384;
	margin-bottom: 1rem;
}

.celebration-img {
	max-width: 100%;
	border-radius: 16px;
	margin-top: 1.5rem;
	min-height: 0;
	object-fit: contain;
}
</style>
