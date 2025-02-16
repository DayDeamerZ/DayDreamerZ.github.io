```HTML
<div id="banner-area">
    <div id="banners-img">
      <div class="img-area">
        <img src="./resourse/wei4.jpg" alt="轮播图" />
      </div>
      <div class="img-area">
        <img src="./resourse/wei1.jpg" alt="轮播图" />
      </div>
      <div class="img-area">
        <img src="./resourse/wei2.jpg" alt="轮播图" />
      </div>
      <div class="img-area">
        <img src="./resourse/wei5.jpg" alt="轮播图" />
      </div>
      <div class="img-area">
        <img src="./resourse/wei3.jpg" alt="轮播图" />
      </div>
    </div>
```

```CSS3
#banner-area {
    position: relative;
    width: 100%;
    height: 200px;
    margin-bottom: 0.5rem;
    border-radius: 0.2rem;
    overflow: hidden;
  }
  #banner-area #banners-img {
    width: 500%;
    height: 100%;
  }
  #banner-area #banners-img .img-area {
    float: left;
    width: 20%;
    height: 100%;
    cursor: pointer;
  }
  #banner-area #banners-img .img-area img {
    width: 100%;
    height: 100%;
  }
  #banner-area #banner-nav {
    position: absolute;
    bottom: 0.3rem;
    right: 0.3rem;
    width: 1.5rem;
    height: 0.8rem;
    font-size: 0.3rem;
    line-height: 0.8rem;
    text-align: center;
    color: rgb(255, 255, 255);
    background-color: rgba(0, 0, 0, 0.3);
    border-radius: 0.4rem;
  }
```

