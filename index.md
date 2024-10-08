---
layout: base
title: Student Home 
description: Home Page
image: /images/mario_animation.png
hide: true
---
          
<table>
  <tr>
    <th>
      <a href="{{site.baseurl}}/">
        <img src="{{site.baseurl}}/images/favicon.ico" width="60" height="60">
      </a>
    </th>
    <th><h3><a href="{{site.baseurl}}/2024/09/20/emojinewspaper_IPYNB_2_.html"> Emoji & Newspaper </a></h3></th>
    <th><h3><a href="{{site.baseurl}}/2024/09/18/RandomNumb.html"> Random Number </a></h3></th>
    <th><h3><a href="{{site.baseurl}}/2024/09/16/Calculator.html"> JS Calculator </a></h3></th>
    <th><h3><a href="{{site.baseurl}}/2024/09/16/Snake.html"> Snake Game </a></h3></th>
    <th><h3><a href="{{site.baseurl}}/2024/09/20/CookieClicker.html"> Cookie Clicker </a></h3></th>
  </tr>
</table>

<div style="border: 2px solid white">
<p> Hello </p>
<button>
Button
</button>
<br>
</div>


<br>

<div style="border: 2px solid white">
  <p> Micheal Jordan vs Lebron James </p>
  <a href="https://www.sportingnews.com/us/nba/news/michael-jordan-vs-lebron-james-goat-debate/sl8xdozy5u1m1s4t5m3npeqo1">
  <button type="button">MJ vs LBJ
  </button>
</a>

<p> Christiano Ronaldo vs Lionel Messi </p>
<a href="https://www.messivsronaldo.app/">
<button type="button">CR7 vs La Pulga
</button>
</a>
</div>

<br>

<div style="border: 2px solid white">
  <p> Shortcuts </p>
  
  <a href="https://github.com/nighthawkcoders/teacher_portfolio">
  <button type="button"> teacher_portfolio
  </button>
  </a>

  <a href="https://github.com/nighthawkcoders/portfolio_2025">
  <button type="button"> portfolio_2025
  </button>
  </a>

  <a href="https://github.com/yuvabala09/yuvabalacsprepo">
  <button type="button"> yuvabalacsprepo
  </button>
  </a>

  <a href="https://app.slack.com/client/TUDAF53UJ/CUS8E3M6Z?cdn_fallback=1">
  <button type="button"> csp_slack
  </button>
  </a>

  <a href="https://github.com/yuvabala09/Yuva-CSSE-Repo">
  <button type="button"> yuva_csse_repo
  </button>
  </a>

</div>


<!--- Concatenation of simakete URL to frontmatter image  --->
{% assign sprite_file = site.baseurl | append: page.image %}
<!--- Has is a list variable containing mario metadata for sprite --->
{% assign hash = site.data.mario_metadata %}  
<!--- Size width/height of Sprit images --->
{% assign pixels = 256 %}

<!--- HTML for page contains <p> tag named "Mario" and class properties for a "sprite"  -->

<p id="mario" class="sprite"></p>
  
<!--- Embedded Cascading Style Sheet (CSS) rules, 
        define how HTML elements look 
--->
<style>

  /*CSS style rules for the id and class of the sprite...
  */
  .sprite {
    height: {{pixels}}px;
    width: {{pixels}}px;
    background-image: url('{{sprite_file}}');
    background-repeat: no-repeat;
  }

  /*background position of sprite element
  */
  #mario {
    background-position: calc({{animations[0].col}} * {{pixels}} * -1px) calc({{animations[0].row}} * {{pixels}}* -1px);
  }
</style>

<!--- Embedded executable code--->
<script>
  ////////// convert YML hash to javascript key:value objects /////////

  var mario_metadata = {}; //key, value object
  {% for key in hash %}  
  
  var key = "{{key | first}}"  //key
  var values = {} //values object
  values["row"] = {{key.row}}
  values["col"] = {{key.col}}
  values["frames"] = {{key.frames}}
  mario_metadata[key] = values; //key with values added

  {% endfor %}

  ////////// game object for player /////////

  class Mario {
    constructor(meta_data) {
      this.tID = null;  //capture setInterval() task ID
      this.positionX = 0;  // current position of sprite in X direction
      this.currentSpeed = 0;
      this.marioElement = document.getElementById("mario"); //HTML element of sprite
      this.pixels = {{pixels}}; //pixel offset of images in the sprite, set by liquid constant
      this.interval = 100; //animation time interval
      this.obj = meta_data;
      this.marioElement.style.position = "absolute";
    }

    animate(obj, speed) {
      let frame = 0;
      const row = obj.row * this.pixels;
      this.currentSpeed = speed;

      this.tID = setInterval(() => {
        const col = (frame + obj.col) * this.pixels;
        this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
        this.marioElement.style.left = `${this.positionX}px`;

        this.positionX += speed;
        frame = (frame + 1) % obj.frames;

        const viewportWidth = window.innerWidth;
        if (this.positionX > viewportWidth - this.pixels) {
          document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
        }
      }, this.interval);
    }

    startWalking() {
      this.stopAnimate();
      this.animate(this.obj["Walk"], 3);
    }

    startRunning() {
      this.stopAnimate();
      this.animate(this.obj["Run1"], 6);
    }

    startPuffing() {
      this.stopAnimate();
      this.animate(this.obj["Puff"], 0);
    }

    startCheering() {
      this.stopAnimate();
      this.animate(this.obj["Cheer"], 0);
    }

    startFlipping() {
      this.stopAnimate();
      this.animate(this.obj["Flip"], 0);
    }

    startResting() {
      this.stopAnimate();
      this.animate(this.obj["Rest"], 0);
    }

    stopAnimate() {
      clearInterval(this.tID);
    }
  }

  const mario = new Mario(mario_metadata);

  ////////// event control /////////

  window.addEventListener("keydown", (event) => {
    if (event.key === "ArrowRight") {
      event.preventDefault();
      if (event.repeat) {
        mario.startCheering();
      } else {
        if (mario.currentSpeed === 0) {
          mario.startWalking();
        } else if (mario.currentSpeed === 3) {
          mario.startRunning();
        }
      }
    } else if (event.key === "ArrowLeft") {
      event.preventDefault();
      if (event.repeat) {
        mario.stopAnimate();
      } else {
        mario.startPuffing();
      }
    }
  });

  //touch events that enable animations
  window.addEventListener("touchstart", (event) => {
    event.preventDefault(); // prevent default browser action
    if (event.touches[0].clientX > window.innerWidth / 2) {
      // move right
      if (currentSpeed === 0) { // if at rest, go to walking
        mario.startWalking();
      } else if (currentSpeed === 3) { // if walking, go to running
        mario.startRunning();
      }
    } else {
      // move left
      mario.startPuffing();
    }
  });

  //stop animation on window blur
  window.addEventListener("blur", () => {
    mario.stopAnimate();
  });

  //start animation on window focus
  window.addEventListener("focus", () => {
     mario.startFlipping();
  });

  //start animation on page load or page refresh
  document.addEventListener("DOMContentLoaded", () => {
    // adjust sprite size for high pixel density devices
    const scale = window.devicePixelRatio;
    const sprite = document.querySelector(".sprite");
    sprite.style.transform = `scale(${0.4 * scale})`;
    mario.startResting();
  });

</script>