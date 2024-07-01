<script>
export default {
	name: 'IntroduceModal',
	props: {
		show: {
			type: Boolean,
			required: true
		}
	},
	emits: ['close'],
	data() {
		return {
			currentPage: 1,
			totalPages: 4
		};
	},
	methods: {
		closeModal() {
			this.$emit('close');
		},
		nextPage() {
			if (this.currentPage < this.totalPages) {
				this.currentPage++;
			}else {
				this.currentPage = 1;
			}
		},
		prevPage() {
			if (this.currentPage > 1) {
				this.currentPage--;
			}else {
				this.currentPage = this.totalPages;
			}
		}
	}
}
</script>

<template>
  <div
    v-if="show"
    class="modal-background"
    @click.self="closeModal"
  >
    <div class="modal-content">
      <div
        v-if="currentPage === 1"
        class="intro-page"
      >
        <h2>訪客登入</h2>
        <img
          class="intro-page-image"
          src="../../assets/images/intro1.png"
        >
      </div>
	  
      <div
        v-if="currentPage === 2"
        class="intro-page"
      >
        <h2>收藏組件</h2>
        <img
          class="intro-page-image"
          src="../../assets/images/intro2.png"
        >
      </div>

      <div
        v-if="currentPage === 3"
        class="intro-page"
      >
        <h2>建立個人儀表板</h2>
        <img
          class="intro-page-image"
          src="../../assets/images/intro3.png"
        >
      </div>

      <div
        v-if="currentPage === 4"
        class="intro-page"
      >
        <h2>刪除個人儀表板</h2>
        <img
          class="intro-page-image"
          src="../../assets/images/intro4.png"
        >
      </div>

      <div class="modal-footer">
        <button
          :disabled="currentPage === 1"
          @click="prevPage"
        >
          ←
        </button>

        <p class="pageNum">
          {{ currentPage }}
        </p>

        <button
          :disabled="currentPage === totalPages"
          @click="nextPage"
        >
          →
        </button>
      </div>
    </div>
  </div>
</template>
  
<style scoped lang="scss">
.modal-background {
	position: fixed;
	top: 0;
	left: 0;
	width: 100vw;
	height: 100vh;
	background-color: rgba(0, 0, 0, 0.5);
	display: flex;
	justify-content: center;
	align-items: center;
	z-index: 1000;
}
  
.modal-content {
	width: fit-content;
	height: fit-content;
	background-color: var(--color-component-background);
	padding: 10px;
	border-radius: 10px;
	overflow: auto;
}

.modal-footer {
	height: 65px;
	display: flex;
	justify-content: space-between;
}

button {
	padding: 10px 20px;
	font-size: 40px;
}

.pageNum {
	padding-top: 27px;
	font-size: 20px;
}

.intro-page {
	width: fit-content;
	height: fit-content;
	overflow: auto;
	text-align: center;

	&-image {
		margin-top: 20px;
		width: 700px;
		height: 400px;
	}
}


</style>
  