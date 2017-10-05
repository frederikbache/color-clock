<template>
  <div id="app" :style="{background: gradient}">
    <h1 class="clock">{{hoursPadded}}:{{minutesPadded}}:{{secondsPadded}}</h1>
  </div>
</template>

<script>
  export default {
    name: 'color-clock',
    data () {
      return {
        config: {
          saturation: 0.65,
          value: 1
        },
        seconds: 0,
        minutes: 0,
        hours: 0,
        hourAngle: 0,
        minuteAngle: 0,
        secondAngle: 0
      }
    },
    computed: {
      hoursGradient () {
        return this.linearGradient(this.hourAngle, 49, 9, 80)
      },
      minutesGradient () {
        return this.linearGradient(this.minuteAngle, 210, 23, 80)
      },
      secondsGradient () {
        return this.linearGradient(this.secondAngle, 320, 25, 100)
      },
      gradient () {
        return this.secondsGradient + ', ' + this.minutesGradient + ', ' + this.hoursGradient
      },
      hoursPadded () {
        return this.pad(this.hours)
      },
      minutesPadded () {
        return this.pad(this.minutes)
      },
      secondsPadded () {
        return this.pad(this.seconds)
      }
    },
    created () {
      // Start the ticker
      setInterval(() => {
        this.tick()
      }, 50)
    },
    methods: {
      /**
       * Adds a leading zero to numbers less than 10
       * @param  {int}    no The number to pad
       * @return {string}    The padded number
       */
      pad (no) {
        return no < 10 ? '0' + no : '' + no
      },
      /**
       * This function handles updating seconds, minutes and hours.
       * It also updates the 'angles' of the current time
       * @return void
       */
      tick () {
        let d = new Date()
        // Save the time of day (for the clock)
        this.seconds = d.getSeconds()
        this.minutes = d.getMinutes()
        this.hours = d.getHours()
        // Calculate the angles (0 - 1)
        let ms = d.getMilliseconds() / 1000
        this.secondAngle = (this.seconds + ms) / 60
        this.minuteAngle = (this.minutes + this.secondAngle) / 60
        this.hourAngle = ((this.hours + this.minuteAngle) % 12) / 12
      },
      /**
       * Utility function that creates a gradient from a hue (from opacity 0 to 1)
       * @param  {number} hue   The hue
       * @param  {int}    angle The desired angle
       * @param  {int}    stop1 The first color stop (in percentage)
       * @param  {int}    stop2 The second color stop
       * @return {string}       The created gradient
       */
      linearGradient (hue, angle, stop1, stop2) {
        return 'linear-gradient(' + angle + 'deg, ' +
               this.hueToRgba(hue, 0) + ' ' + stop1 + '%,' +
               this.hueToRgba(hue, 1) + ' ' + stop2 + '%)'
      },
      /**
       * Creates an rgba color string from a hue and desired alpha
       * @param  {float} h The desired hue
       * @param  {float} a The desired alpha
       * @return {strin}   The rgba css string
       */
      hueToRgba (h, a) {
        let rgb = this.hsvToRgb(h, this.config.saturation, this.config.value)
        return 'rgba(' + rgb.r + ', ' + rgb.g + ', ' + rgb.b + ', ' + a + ')'
      },
      /**
       * Converts a HSV color to RGB
       *
       * Implementation based on
       * {@link http://axonflux.com/handy-rgb-to-hsl-and-rgb-to-hsv-color-model-c}
       *
       * @param  {float} h  The hue
       * @param  {float} s  The saturation
       * @param  {float} v  The value
       * @return {object}   An object containing the r, g and b values
       */
      hsvToRgb (h, s, v) {
        var r, g, b, i, f, p, q, t
        i = Math.floor(h * 6)
        f = h * 6 - i
        p = v * (1 - s)
        q = v * (1 - f * s)
        t = v * (1 - (1 - f) * s)
        switch (i % 6) {
          case 0: r = v; g = t; b = p; break
          case 1: r = q; g = v; b = p; break
          case 2: r = p; g = v; b = t; break
          case 3: r = p; g = q; b = v; break
          case 4: r = t; g = p; b = v; break
          case 5: r = v; g = p; b = q; break
        }
        return {
          r: Math.round(r * 255),
          g: Math.round(g * 255),
          b: Math.round(b * 255)
        }
      }
    }
  }
</script>

<style>
body, html {
  padding: 0;
  margin: 0;
  height: 100%;
}
#app {
  width: 100%;
  height: 100%;
}
.clock {
  font-family: 'Fira Mono', monospace;
  padding: 0;
  margin: 0;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  color: #fff;
  font-size: 80px;
  font-weight: 500;
  opacity: 0.7;
}

@media (max-width: 450px) {
  .clock {
    font-size: 55px;
  }
}
</style>
