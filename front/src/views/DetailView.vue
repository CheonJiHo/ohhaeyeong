<template>
  <div>
    <div class="movie-details">
      <div class="poster">
        <img v-if="movie && movie.poster_path" :src="`https://image.tmdb.org/t/p/original${movie.poster_path}`" alt="image">
      </div>
      <div class="details">
        <h2>{{ movie?.title }}</h2>
        <p><strong>평점:</strong> {{ movie?.vote_average }}</p>
        <p><strong>개봉일:</strong> {{ movie?.release_date }}</p>
        <p><strong>줄거리:</strong> {{ movie?.overview }}</p>
      </div>
    </div>

    <!-- 트레일러 영상 -->
    <div class="trailer">
      <!-- 트레일러 영상을 여기에 추가 -->
      <MovieTrailer :movie="movie"/>
    </div>

    <!-- 리뷰 표시 -->
    <div class="reviews">
      <h3>리뷰</h3>
      <div v-for="review in reviews" :key="review.id" class="review-item">
        <p><strong>작성자:</strong> {{ review.user.username }}</p>
        <p>{{ review.content }}</p>
        <!-- 작성자와 로그인한 사용자가 같을 때만 수정과 삭제 버튼을 보여줌 -->
        <div v-if="review.user.id === userpk" class="inline">
          <button @click.prevent="updateReview(review)" class="btn btn-secondary" style="font-size: 0.5rem;">수정</button>
          <button @click.prevent="deleteReview(review)" class="btn btn-danger" style="font-size: 0.5rem;">삭제</button>
        </div>
      </div>
    </div>

    <!-- 리뷰 작성 폼 -->
    <form class="review-form">
      <h3>
        <input type="text" v-model="review_content" placeholder="리뷰 작성" class="rounded-input">
        <button @click.prevent="reviewCreate" class="btn btn-primary">제출</button>
      </h3>
    </form>
  </div>
</template>

<script>
import axios from 'axios';
import MovieTrailer from '@/components/MovieTrailer.vue'
export default {
  name: 'DetailView',
  components: {
    MovieTrailer,
  },
  data() {
    return {
      movie: null,
      userName: '',
      userpk: null,
      reviews: [],
      movieId: null,
      token: null,
      review_content: '',
    };
  },
  created() {
    this.movieId = this.$route.params.id;
    this.fetchMovieData();
    this.currentUser();
    this.getReviews();
  },
  methods: {
    fetchMovieData() {
      axios
        .get(`http://127.0.0.1:8000/api/v1/movies/${this.movieId}/`)
        .then(response => {
          this.movie = response.data;
        })
        .catch(error => {
          console.error(error);
        });
    },
    currentUser() {
      const token = this.$store.state.token;
      axios
        .get('http://127.0.0.1:8000/accounts/user/', {
          headers: {
            Authorization: `Token ${token}`,
          },
        })
        .then(response => {
          this.userpk = response.data.pk;
          this.userName = response.data.username;
        })
        .catch(error => {
          console.error(error);
        });
    },
    getReviews() {
      axios({
        method: 'get',
        url: `http://127.0.0.1:8000/api/v1/movies/reviews/${this.movieId}/`,
        headers: {
          Authorization: `Token ${this.$store.state.token}`,
        },
      })
        .then(res => {
          this.reviews = res.data;
          console.log(this.reviews);
        })
        .catch(err => {
          console.log(err);
        });
    },
    reviewCreate() {
      const reviewItem = {
        content: this.review_content,
      };
      if (reviewItem) {
        axios({
          method: 'post',
          url: `http://127.0.0.1:8000/api/v1/movies/${this.movieId}/reviews/`,
          data: reviewItem,
          headers: {
            Authorization: `Token ${this.$store.state.token}`,
          },
        })
          .then(() => {
            this.review_content = '';
            this.getReviews();
          })
          .catch(err => {
            console.log(err);
          });
      }
    },
    updateReview(review) {
      const reviewItem = {
        content: this.review_content,
      };
      axios({
        method: 'put',
        url: `http://127.0.0.1:8000/api/v1/reviews/${review.id}/`,
        data: reviewItem,
        headers: {
          Authorization: `Token ${this.$store.state.token}`,
        },
      })
        .then(res => {
          console.log(res);
          this.review_content = '';
          this.getReviews();
        })
        .catch(err => {
          console.log(err);
        });
    },
    deleteReview(review) {
      axios({
        method: 'delete',
        url: `http://127.0.0.1:8000/api/v1/reviews/${review.id}/`,
        headers: {
          Authorization: `Token ${this.$store.state.token}`,
        },
      })
        .then(res => {
          console.log(res);
          this.getReviews();
        })
        .catch(err => {
          console.log(err);
        });
    },
  },
};
</script>

<style scoped>
.movie-details {
  display: flex;
  margin-bottom: 20px;
}

.poster {
  margin-right: 20px;
}

.poster img {
  max-width: 500px;
  
}

.details {
  flex: 1;
}

.review-item {
  margin-bottom: 10px;
}

.inline {
  display: inline-block;
  margin-left: 10px;
}

.review-form {
  margin-top: 20px;
}

.review-form input[type='text'] {
  width: 300px;
}

.btn {
  padding: 0.2rem 0.5rem;
}

.rounded-input {
  border-radius: 20px;
  text-align: center;
  /* Add any other styles you want for the input */
}
</style>
