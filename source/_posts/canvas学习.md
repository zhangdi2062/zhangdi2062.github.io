---
title: Canvas学习
tags:
  - js
  - canvas
categories:
  - 前端
date: 2024-03-01 21:11:20
---

## 在画布上绘制矩形并拖动

```javascript
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

class Rect {
    constructor(color, startX, startY) {
        this.color = color;
        this.startX = startX;
        this.startY = startY;
        this.endX = startX;
        this.endY = startY;
    }
    get mix() {
        return Math.min(this.startX, this.endX);
    }
    get miy() {
        return Math.min(this.startY, this.endY);
    }
    get max() {
        return Math.max(this.startX, this.endX);
    }
    get may() {
        return Math.max(this.startY, this.endY);
    }
    draw() {
        ctx.beginPath();
        ctx.fillStyle = this.color;
        ctx.moveTo(this.startX, this.startY);
        ctx.lineTo(this.startX, this.endY)
        ctx.lineTo(this.endX, this.endY)
        ctx.lineTo(this.endX, this.startY)
        ctx.lineWidth = 3;
        ctx.strokeStyle = '#fff';
        ctx.stroke();
        ctx.fill();
    }
    isInside(x, y) {
        return x > this.mix && x < this.max && y > this.miy && y < this.may;
    }
}

// new Rect('#f00', 10, 100, 300, 400).draw()

function getShape(x, y) {
    for (let i = store.length - 1; i >= 0; i--) {
        const shape = store[i];
        if (shape.isInside(x, y)) {
            return shape;
        }
    }
    return null;
}
let isDrag = false;
const store = [];
const rect = canvas.getBoundingClientRect()
canvas.addEventListener('mousedown', (e) => {
    const x = e.clientX - rect.x
    const y = e.clientY - rect.y
    // 判断是否选中了形状
    const isInside = getShape(x, y)
    if (isInside) {
        const { startX, startY, endX, endY } = isInside;
        const moveFn = (e1) => {
            isInside.startX = startX + e1.clientX - e.clientX;
            isInside.startY = startY + e1.clientY - e.clientY;
            isInside.endX = endX + e1.clientX - e.clientX;
            isInside.endY = endY + e1.clientY - e.clientY;
            isDrag = true;
        }
        const upFn = () => {
            isDrag = false;
            canvas.removeEventListener('mousemove', moveFn)
            canvas.removeEventListener('mouseup', upFn)
        }
        canvas.addEventListener('mousemove', moveFn)
        canvas.addEventListener('mouseup', upFn)
    } else {
        // 没选中
        const shape = new Rect('#f00', x, y);
        store.push(shape);
        const moveFn = (e1) => {
            isDrag = true;
            const x1 = e1.clientX - rect.x
            const y1 = e1.clientY - rect.y
            shape.endX = x1;
            shape.endY = y1;
        }
        const upFn = () => {
            console.error('up');
            isDrag = false;
            canvas.removeEventListener('mousemove', moveFn)
            canvas.removeEventListener('mouseup', upFn)
        }
        canvas.addEventListener('mousemove', moveFn)
        canvas.addEventListener('mouseup', upFn)
    }

})

let timer = null;
function draw() {
    timer = requestAnimationFrame(() => {
        if (isDrag) {
            console.log('hi')
            ctx.clearRect(0, 0, 400, 600);
            store.forEach((item) => {
                item.draw();
            })
        }
        draw()
    })
}

draw()
```

## 博客风格的点线动画

```javascript
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
function getRandom(min, max) {
    const random = Math.random();
    return Math.floor(min + (max - min + 1) * random);
}

function init() {
    canvas.width = window.innerWidth * devicePixelRatio;
    canvas.height = window.innerHeight * devicePixelRatio;
}

init();
class Point {
    constructor() {
        this.r = 6;
        this.x = getRandom(0, canvas.width - this.r / 2)
        this.y = getRandom(0, canvas.height - this.r / 2)
        this.xSpeed = getRandom(-50, 50);
        this.ySpeed = getRandom(-50, 50);
        this.time = null;
    }

    draw() {
        if (this.time) {
            const now = new Date().getTime();
            const t = (now - this.time) / 1000;
            this.x += this.xSpeed * t;
            this.y += this.ySpeed * t;
            if (this.x > canvas.width - this.r / 2) {
                this.xSpeed = -this.xSpeed;
                this.x = canvas.width - this.r / 2;
            } else if (this.x < this.r / 2) {
                this.xSpeed = -this.xSpeed;
                this.x = 0 + this.r / 2;
            }
            if (this.y > canvas.height - this.r / 2) {
                this.ySpeed = -this.ySpeed;
                this.y = canvas.height - this.r / 2;
            } else if (this.y < this.r / 2) {
                this.ySpeed = -this.ySpeed;
                this.y = 0 + this.r / 2;
            }
        }
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI)
        ctx.fillStyle = 'rgb(200, 200, 200)';
        ctx.fill();
        this.time = new Date().getTime();
    }
}

class Graph {
    constructor(pointNumber = 30, maxDis = 500) {
        this.points = new Array(pointNumber).fill(0).map(() => new Point());
        this.maxDis = maxDis;
    }

    draw() {
        requestAnimationFrame(() => this.draw());
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        for (let i = 0; i < this.points.length; i++) {
            const p1 = this.points[i];
            p1.draw();

            for(let j = i + 1; j < this.points.length; j++) {
                const p2 = this.points[j];
                const d = Math.sqrt(Math.abs(p1.x - p2.x) ** 2 + Math.abs(p1.y - p2.y) ** 2);
                if (d > this.maxDis) {
                    continue;
                }
                ctx.beginPath();
                ctx.moveTo(p1.x, p1.y);
                ctx.lineTo(p2.x, p2.y);
                ctx.closePath();
                ctx.strokeStyle = `rgba(200, 200, 200, ${1- d / this.maxDis})`
                ctx.stroke();
            }
        }
    }
}
new Graph().draw();
```