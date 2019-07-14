<template>
  <div class="yt-lazy">
    <a class="yt-lazy__link" v-bind:href="'https://youtu.be/'+videoId" target="_blank" v-if="!loaded">
      <picture class="yt-lazy__pic">
        <source v-if="webpSupported === true && webpAvailable === true"
                v-bind:srcset="'https://i.ytimg.com/vi_webp/'+videoId+'/'+usedImage+'.webp'" type="image/webp">
        <img class="yt-lazy__img" v-bind:src="'https://i.ytimg.com/vi/'+videoId+'/'+usedImage+'.jpg'">
      </picture>
      <button class="yt-lazy__btn" v-on:click="run" type="button" aria-label="Start video">
        <svg width="68" height="48" viewBox="0 0 68 48">
          <path class="yt-lazy__btn-shape"
                d="M66.52,7.74c-0.78-2.93-2.49-5.41-5.42-6.19C55.79,.13,34,0,34,0S12.21,.13,6.9,1.55 C3.97,2.33,2.27,4.81,1.48,7.74C0.06,13.05,0,24,0,24s0.06,10.95,1.48,16.26c0.78,2.93,2.49,5.41,5.42,6.19 C12.21,47.87,34,48,34,48s21.79-0.13,27.1-1.55c2.93-0.78,4.64-3.26,5.42-6.19C67.94,34.95,68,24,68,24S67.94,13.05,66.52,7.74z"></path>
          <path class="yt-lazy__btn-icon" d="M 45,24 27,14 27,34"></path>
        </svg>
      </button>
    </a>
    <iframe v-if="loaded" width="560" height="315" class="yt-lazy__frame"
            v-bind:src="'https://www.youtube.com/embed/'+videoId+'?rel=0&showinfo=0&autoplay=1'" frameborder="0"
            allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
</template>

<script>
  const sizes = ['maxresdefault', 'hqdefault', 'mqdefault', 'sddefault', 'default'];
  export default {
    name: "yt-lazy",
    props: ['videoId', 'postTitle'],
    data: () => {
      return {
        usedImage: 'default',
        loaded: false,
        webpSupported: false,
        webpAvailable: false,
      }
    },
    created: function () {

    },
    mounted: function () {
      this.getAvailableImage().then(() => {
        this.supportWebp().then(() => {
            this.getAvailableWebp().then((val) => {
              console.log(val);
              this.webpAvailable = true;
            })
          }
        ).catch((err) => {
        });
      });
    },
    methods: {
      getAvailableImage() {

        const _this = this;

        function firstInSequence(values, asyncFn) {

          return new Promise(function (resolve, reject) {

            if (values.length === 0) {
              reject();
            }
            asyncFn(values[0]).then(function (val) {
              resolve(val);
            }).catch(function () {
              if (values.length !== 0) {
                values.shift();
                firstInSequence(values, asyncFn).then(resolve).catch(reject);
              }
            });
          });
        }

        function yourAsyncFunction(val) {
          return new Promise((resolve, reject) => {
            _this.checkImage(val).then((el) => {
              resolve(val);
            }).catch(err => {
              reject(val)
            });
          });
        }

        return new Promise((resolve, reject) => {
          let seqSizes = sizes.slice(0);
          firstInSequence(seqSizes, yourAsyncFunction).then(function (val) {
            _this.usedImage = val;
            resolve(val)
          }).catch(function () {
            reject();
            console.log('All images rejected, set to default');
          });
        });
      },
      getAvailableWebp() {
        return new Promise((resolve, reject) => {
          this.checkImage(this.usedImage, true).then((el) => {
            resolve(this.usedImage);
          }).catch(err => {
            reject(this.usedImage)
          });
        });

      },
      checkImage(size, webp = false) {
        return new Promise((resolve, reject) => {
          let img = new Image();
          img.addEventListener('load', () => {
            if(webp){
              console.log(img.naturalHeight);
            }
            if (img.naturalHeight > 90) {
              resolve(img)
            } else {
              reject(img);
            }
          });
          img.addEventListener('error', () => {
            reject(new Error(`Failed to load image's URL: ${url}`));
          });
          let vid = this.videoId;
          if (webp) {
            img.src = `https://i.ytimg.com/vi_webp/${vid}/${size}.webp`
          } else {
            img.src = `https://i.ytimg.com/vi/${vid}/${size}.jpg`;
          }
        });
      },

      run(e) {
        e.preventDefault();
        this.loaded = true;
      },

      supportWebp() {
        const _this = this;
        return new Promise((resolve, reject) => {
          const ls = localStorage.getItem('supportWebp');
          if (ls) {
          _this.webpSupported = new Boolean(ls);
            resolve(ls)
          }
          let webP = new Image();
          webP.src = 'data:image/webp;base64,UklGRi4AAABXRUJQVlA4TCEAAAAvAUAAEB8wA' + 'iMwAgSSNtse/cXjxyCCmrYNWPwmHRH9jwMA';
          webP.onload = webP.onerror = function () {
            if (webP.height === 2) {
              localStorage.setItem('supportWebp', 1);
              _this.webpSupported = true;
              resolve(true);
            } else {
              reject(false);
            }

          };

        })
      }
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

  .yt-lazy__pic {
    display: block;
    height: 100%;
    width: 100%;
  }

  .yt-lazy__img {
    width: 100%;
    height: auto;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    position: absolute;
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
