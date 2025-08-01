<template>
	<div
		ref="cardRef"
		:class="`card-container  rounded-2xl ${imageSize} ${isAnimation ? 'card-3d cursor-pointer' : ''} 
		max-[500px]:w-full max-[500px]:h-[48vw]`"
		:style="cardStyle"
		@mousemove="handleMouseMove"
		@mouseleave="handleMouseLeave"
		@click="handleCardClick"
		@touchstart="handleTouchStart"
		@touchmove="handleTouchMove"
		@touchend="handleTouchEnd"
	>
		<SnapshotFront
			v-if="!props.isAnimation"
			:characterInfo="props.characterInfo"
			:sizeType="props.sizeType"
			:rotateValue="flipRotation"
			:isAnimation="props.isAnimation"
			:userName="displayUserName"
			:ssrUserName="props.ssrUserName"
			:isCapture="props.isCapture"
		/>

		<div v-else class="card-inner">
			<SnapshotFront
				:characterInfo="props.characterInfo"
				:sizeType="props.sizeType"
				:rotateValue="flipRotation"
				:isAnimation="props.isAnimation"
				:userName="displayUserName"
				:ssrUserName="props.ssrUserName"
				:isCapture="props.isCapture"
			/>

			<SnapshotBack :ssrUserName="props.ssrUserName" :flipRotation="flipRotation" />
		</div>
	</div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { getUserName } from '@/util/sessionStorage';
import { getCharacterImageName } from '@/util/characterUtils';

import SnapshotFront from '@/components/card/SnapshotFront.vue';
import SnapshotBack from '@/components/card/SnapshotBack.vue';

const props = defineProps({
	isAnimation: {
		type: Boolean,
		default: false
	},
	isCapture: {
		type: Boolean,
		default: false
	},
	characterInfo: {
		type: Object
	},
	sizeType: {
		type: String
	},
	userName: {
		type: String,
		default: ''
	},
	ssrUserName: {
		type: String
	}
});

const cardRef = ref(null);
const rotationX = ref(0);
const rotationY = ref(0);
const flipRotation = ref(0);

// 터치 위치 저장용
let touchStartX = 0;
let touchStartY = 0;

// 마우스 이동 처리
function handleMouseMove(event) {
	if (!props.isAnimation || !cardRef.value) return;

	const rect = cardRef.value.getBoundingClientRect();
	const centerX = rect.left + rect.width / 2;
	const centerY = rect.top + rect.height / 2;

	const mouseX = event.clientX - centerX;
	const mouseY = event.clientY - centerY;

	// 회전 각도 계산 (최대 20도)
	const maxRotation = 20;
	rotationX.value = (mouseY / (rect.height / 2)) * -maxRotation;
	rotationY.value = (mouseX / (rect.width / 2)) * maxRotation;
}

// 마우스가 카드를 벗어날 때 원래 위치로 복원
function handleMouseLeave() {
	if (!props.isAnimation) return;

	rotationX.value = 0;
	rotationY.value = 0;
}

// 터치 시작
function handleTouchStart(event) {
	if (!props.isAnimation || !cardRef.value) return;
	if (event.touches.length !== 1) return;

	const touch = event.touches[0];
	touchStartX = touch.clientX;
	touchStartY = touch.clientY;
}

// 터치 이동
function handleTouchMove(event) {
	if (!props.isAnimation || !cardRef.value) return;
	if (event.touches.length !== 1) return;

	const rect = cardRef.value.getBoundingClientRect();
	const centerX = rect.left + rect.width / 2;
	const centerY = rect.top + rect.height / 2;

	const touch = event.touches[0];
	const touchX = touch.clientX - centerX;
	const touchY = touch.clientY - centerY;

	const maxRotation = 20;
	rotationX.value = (touchY / (rect.height / 2)) * -maxRotation;
	rotationY.value = (touchX / (rect.width / 2)) * maxRotation;
}

// 터치 종료 시 각도 초기화
function handleTouchEnd() {
	if (!props.isAnimation) return;
	rotationX.value = 0;
	rotationY.value = 0;
}

// 카드 클릭 처리
function handleCardClick() {
	if (!props.isAnimation) return;

	// 180도 회전 (앞면 ↔ 뒷면)
	flipRotation.value = flipRotation.value === 0 ? 180 : 0;
}

// 3D 변환 스타일 계산
const cardStyle = computed(() => {
	if (!props.isAnimation) return {};

	const transform = `perspective(1000px) rotateX(${rotationX.value}deg) rotateY(${rotationY.value}deg)`;

	return {
		transform
	};
});

function getImageSize() {
	if (props.sizeType === 'sm') {
		return 'w-[420px] h-[237px]';
	}

	if (props.sizeType === 'default') {
		return 'w-[500px] h-[281px]';
	}

	return 'w-[420px] h-[237px]';
}

const imageSize = getImageSize();

const displayUserName = computed(() => props.userName || getUserName());

// 외부에서 접근할 수 있도록 expose
defineExpose({
	rotationX,
	rotationY,
	flipRotation
});
</script>
