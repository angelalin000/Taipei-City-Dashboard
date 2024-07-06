<script setup>
import { ref , defineEmits } from "vue";
import http from "../../router/axios";
import { useDialogStore } from "../../store/dialogStore";
import { useAuthStore } from "../../store/authStore";
import { useContentStore } from "../../store/contentStore";

const dialogStore = useDialogStore();
const authStore = useAuthStore();
const contentStore = useContentStore();

const emit = defineEmits(['close']);

const allInputs = ref({
	name: "",
	reason: "",
	scope: "",
});

async function handleSubmit() {
	const submitObject = {
		name: allInputs.value.name,
		reason: allInputs.value.reason,
		scope: allInputs.value.scope,
		user_name: authStore.user.name,
		user_id: `${authStore.user.user_id}`,
		status: "待處理",
	};
	await http.post(`/wish/`, submitObject);
	dialogStore.showNotification("success", "願望提交成功，感謝您的建議");
	contentStore.loading = false;
	handleClose();
}

function handleClose() {
	allInputs.value = {
		name: "",
		reason: "",
		scope: "",
	};
	emit('close');
}
</script>

<template>
  <div class="wishcomponent">
    <div
      class="wishcomponent-backdrop"
      @click="handleClose"
    />
    <div class="wishcomponent-dialog">
      <h2>願望提交</h2>
      <h3>組件名稱* ({{ allInputs.name.length }}/20)</h3>
      <input
        v-model="allInputs.name"
        class="wishcomponent-input"
        type="text"
        :minLength="1"
        :maxLength="20"
        required
      >
      <h3>重要的原因* ({{ allInputs.reason.length }}/200)</h3>
      <textarea
        v-model="allInputs.reason"
        :minLength="1"
        :maxLength="200"
        required
      />
      <h3>應用範圍* ({{ allInputs.scope.length }}/200)</h3>
      <textarea
        v-model="allInputs.scope"
        :minLength="1"
        :maxLength="200"
        required
      />
      <div class="wishcomponent-control">
        <button
          class="wishcomponent-control-cancel"
          @click="handleClose"
        >
          取消
        </button>
        <button
          v-if="allInputs.name && allInputs.reason && allInputs.scope"
          class="wishcomponent-control-confirm"
          @click="handleSubmit"
        >
          提交願望
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.wishcomponent {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;

  &-backdrop {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
  }

  &-dialog {
    position: relative;
	width: 400px;
    background: var(--color-component-background);
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    z-index: 1001;
  }

  h3 {
    margin: 0.5rem 0;
    font-size: var(--font-s);
    font-weight: 400;
  }

  &-input,
  textarea {
    width: 385px;
    padding: 8px;
    font-size: var(--font-s);
    border-radius: 4px;
    margin-bottom: 1rem;
    box-sizing: border-box;
  }

  &-control {
    display: flex;
    justify-content: flex-end;
    margin-top: var(--font-ms);

    &-cancel {
      margin: 0 2px;
      padding: 4px 6px;
      border-radius: 5px;
      transition: color 0.2s;

      &:hover {
        color: var(--color-highlight);
      }
    }

    &-confirm {
      margin: 0 2px;
      padding: 4px 10px;
      border-radius: 5px;
      background-color: var(--color-highlight);
      transition: opacity 0.2s;

      &:hover {
        opacity: 0.8;
      }
    }
  }
}
</style>