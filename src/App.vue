<template>
  <div class="yt-lazy">
    <a class="yt-lazy__link" v-bind:href="'https://youtu.be/'+id" target="_blank" v-if="!loaded">
      <picture>
        <source v-bind:srcset="'https://i.ytimg.com/vi_webp/'+id+'/maxresdefault.webp'" type="image/webp">
        <img class="yt-lazy__img" v-bind:src="'https://i.ytimg.com/vi/'+id+'/maxresdefault.jpg'">
      </picture>
      <button class="yt-lazy__btn" v-on:click="run" type="button" aria-label="Запустить видео">
        <svg width="68" height="48" viewBox="0 0 68 48">
          <path class="yt-lazy__btn-shape"
                d="M66.52,7.74c-0.78-2.93-2.49-5.41-5.42-6.19C55.79,.13,34,0,34,0S12.21,.13,6.9,1.55 C3.97,2.33,2.27,4.81,1.48,7.74C0.06,13.05,0,24,0,24s0.06,10.95,1.48,16.26c0.78,2.93,2.49,5.41,5.42,6.19 C12.21,47.87,34,48,34,48s21.79-0.13,27.1-1.55c2.93-0.78,4.64-3.26,5.42-6.19C67.94,34.95,68,24,68,24S67.94,13.05,66.52,7.74z"></path>
          <path class="yt-lazy__btn-icon" d="M 45,24 27,14 27,34"></path>
        </svg>
      </button>
    </a>
    <iframe v-if="loaded" width="560" height="315" class="yt-lazy__frame"
            v-bind:src="'https://www.youtube.com/embed/'+id+'?rel=0&showinfo=0&autoplay=1'" frameborder="0"
            allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
</template>

<script>
  export default {
    name: "yt-lazy",
    props: ['id'],
    data: () => {
      return {
        loaded: false,
      }
    },

    methods: {
      run(e) {
        e.preventDefault();
        this.loaded = true;
      },
    }
  }
</script>

<style>
  .yt-lazy {
    position: relative;
    display: block;
    width: 100%;
    padding: 0;
    overflow: hidden;
    background-color: #5d5d5d;
  }

  .yt-lazy::before {
    content: '';
    display: block;
    padding-top: 56.25%;
  }

  .yt-lazy__img {
    max-width: 100%;
    height: auto;
  }

  .yt-lazy__btn {
    position: absolute;
    top: 0;
    border: 0;
    background: none;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
  }

  .yt-lazy__link,
  .yt-lazy__frame {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: 0;
  }

  .yt-lazy__btn-shape {
    fill: #212121;
    fill-opacity: 0.8;
  }

  .yt-lazy__btn-icon {
    fill: #ffffff;
  }

  .yt-lazy__btn:focus {
    outline: none;
  }

  .yt-lazy:hover .yt-lazy__btn-shape,
  .yt-lazy__btn:focus .yt-lazy__btn-shape {
    fill: #ff0000;
    fill-opacity: 1;
  }
</style>
