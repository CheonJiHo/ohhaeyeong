<template>
  <div>
    <div class="card movie-card" style="width: 15rem;" 
    @click="ToDetail(movie)" @contextmenu.prevent="openModal(movie)">
      <img
        v-bind:src="`https://image.tmdb.org/t/p/original${movie.poster_path}`"
        class="card-img-top"
        alt="image"
        style="object-fit: cover; height: 500px"
      />
      <div class="card-body">
        <h5 class="card-title clamp-two-lines">{{ movie.title }}</h5>
      </div>
    </div>
    <!-- 모달 창 -->
    <div v-if="isModalOpen" class="modal" @click="closeModal">
      <div class="modal-content" @click.stop>
        <img
          v-bind:src="`https://image.tmdb.org/t/p/original${selectedMovie.poster_path}`"
          class="modal-poster"
          alt="image"
        />
        <p class="modal-overview"><strong>overview</strong> <br>
          <span class="overview-text">{{ selectedMovie.overview }}</span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MovieCard',
  props: {
    movie: Object,
  },
  data() {
    return {
      isModalOpen: false,
      selectedMovie: null,
    };
  },
  methods: {
    ToDetail(movie) {
      this.$router.push({
        name: 'DetailView',
        params: {
          id: movie.id,
        },
      });
    },
  openModal(movie) {
    this.selectedMovie = movie;
    this.isModalOpen = true;
  },
  closeModal() {
    this.isModalOpen = false;
  }, 
  },
}
</script>

<style>
.card {
  position: relative;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.modal-content {
  background-color: white;
  padding: 20px;
  max-width: 550px;
  max-height: 1500px;
  overflow-y: hidden;
}


.modal-overview strong {
  font-weight: bold;
  font-size: 2rem;
}

.modal-overview .overview-text {
  font-size: 1rem;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
}
.card-title {
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
