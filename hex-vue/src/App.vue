<template>
  <div>
    <canvas id="canvas" :width="canvasWidth" :height="canvasHeight"> </canvas>
  </div>
</template>

<script>
export default {
  data() {
    return {
      hexSize: 20,
      hexOrgin: { x: 300, y: 300 },
      canvasWidth: 800,
      canvasHeight: 600,
      canvasId: "",
      hexParameters: "",
    };
  },
  mounted() {
    this.canvasId = document.getElementById("canvas");
    this.hexParameters = this.getHexParameters();
    this.drawHexes();
  },
  methods: {
    Point(_x, _y) {
      return { x: _x, y: _y };
    },
    Hex(_q, _r) {
      return { q: _q, r: _r };
    },
    hexToPixel(_hex) {
      let hexOrgin = this.hexOrgin;
      let x =
        this.hexSize * (Math.sqrt(3) * _hex.q + (Math.sqrt(3) / 2) * _hex.r) +
        hexOrgin.x;
      let y = this.hexSize * ((3 / 2) * _hex.r) + hexOrgin.y;
      return this.Point(x, y);
    },
    getHexParameters() {
      let hexHeight = this.hexSize * 2;
      let hexWidth = (Math.sqrt(3) / 2) * hexHeight;
      let vertDist = hexHeight * (3 / 4);
      let horizDist = hexWidth;
      return { hexWidth, hexHeight, vertDist, horizDist };
    },
    getHexCournerCoord(_center, _i) {
      let angle_deg = 60 * _i - 30;
      let angle_rad = (Math.PI / 180) * angle_deg;
      let x = _center.x + this.hexSize * Math.cos(angle_rad);
      let y = _center.y + this.hexSize * Math.sin(angle_rad);
      return this.Point(x, y);
    },
    drawHexCoordinates(_canvasId, _center, _hex) {
      let ctx = _canvasId.getContext("2d");
      ctx.fillText(_hex.q, _center.x - 10, _center.y);
      ctx.fillText(_hex.r, _center.x + 7, _center.y);
    },
    drawLine(_canvasId, _start, _end) {
      let ctx = _canvasId.getContext("2d");
      ctx.beginPath();
      ctx.moveTo(_start.x, _start.y);
      ctx.lineTo(_end.x, _end.y);
      ctx.stroke();
      ctx.closePath();
    },
    drawHex(_canvasId, _center) {
      for (let i = 0; i <= 5; i++) {
        let start = this.getHexCournerCoord(_center, i);
        let end = this.getHexCournerCoord(_center, i + 1);

        this.drawLine(
          _canvasId,
          { x: start.x, y: start.y },
          { x: end.x, y: end.y }
        );
      }
    },
    drawHexes() {
      let hexWidth = this.getHexParameters().hexWidth;
      let hexHeight = this.getHexParameters().hexHeight;
      let hexOrgin = this.hexOrgin;
      let canvasWidth = this.canvasWidth;
      let canvasHeight = this.canvasHeight;

      let qLeftSide = Math.round(hexOrgin.x / hexWidth) * 4;
      let qRightSide = (Math.round(canvasWidth - hexOrgin.x) / hexWidth) * 2;

      let rTopSide = Math.round(hexOrgin.y / (hexHeight / 2));
      let rBottomSide = Math.round(
        (canvasHeight - hexOrgin.y) / (hexHeight / 2)
      );

      for (let r = -rTopSide; r <= rBottomSide; r++) {
        for (let q = -qLeftSide; q <= qRightSide; q++) {
          let center = this.hexToPixel(this.Hex(q, r));
          if (
            center.x > hexHeight / 2 &&
            center.x < canvasWidth - hexWidth / 2 &&
            center.y > hexWidth / 2 &&
            center.y < canvasHeight - hexHeight / 2
          ) {
            this.drawHex(this.canvasId, center);
            this.drawHexCoordinates(this.canvasId, center, this.Hex(q, r));
          }
        }
      }
    },
  },
};
</script>

<style>
canvas {
  border: 1px black solid;
}
</style>
