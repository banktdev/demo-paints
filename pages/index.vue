<template>
    <div>
        <canvas 
            ref="canvas" 
            id="drawing-pad" 
            width="300" 
            height="300">
            This is an interactive drawing pad
        </canvas>
        <div>
            <input type="file" ref="imageInput" @change="uploadBackground" accept="images/*" />
            <button @click="resetCanvas">Reset</button>
            <button @click="saveImage">Save</button>
        </div>
        <img ref="img" src="" id="canvas-image" />
    </div>
</template>

<script>
    export default {
        data() {
            return {
                canvas: null,
                context: null,
                isDrawing: false,
                startX: 0,
                startY: 0,
                points: [],
            }
        },
        mounted() {
            var vm = this
            vm.canvas = vm.$refs.canvas
            vm.context = vm.canvas.getContext('2d')
            vm.canvas.addEventListener('mousedown', vm.mousedown)
            vm.canvas.addEventListener('mousemove', vm.mousemove)
            document.addEventListener('mouseup', vm.mouseup)

        },
        methods: {
            mousedown(e) {
                var vm = this
                var rect = vm.canvas.getBoundingClientRect()
                var x = e.clientX - rect.left
                var y = e.clientY - rect.top

                vm.isDrawing = true
                vm.startX = x
                vm.startY = y
                vm.points.push({ x: x, y: y })
            },
            mousemove(e) {
                var vm = this
                var rect = vm.canvas.getBoundingClientRect()
                var x = e.clientX - rect.left
                var y = e.clientY - rect.top

                if (vm.isDrawing) {
                    vm.context.beginPath()
                    vm.context.moveTo(vm.startX, vm.startY)
                    vm.context.lineTo(x, y)
                    vm.context.stroke()
                    vm.startX = x
                    vm.startY = y
                    vm.points.push({ x: x, y: y })
                }
            },
            mouseup() {
                var vm = this
                vm.isDrawing = false
                if(vm.points.length > 0) {
                    localStorage['points'] = JSON.stringify(vm.points)
                }
            },
            resetCanvas() {
                var vm = this
                vm.canvas.width = vm.canvas.width
                vm.points.length = 0
            },
            uploadBackground(event) {
                console.log('uploadBackground')
                const vm = this;
                const file = event.target.files[0];

                if (file) {
                    const reader = new FileReader();

                    reader.onload = function (e) {
                        const img = new Image();
                        img.src = e.target.result;

                        img.onload = function () {
                            // Set the uploaded image as the background
                            vm.context.drawImage(img, 0, 0, vm.canvas.width, vm.canvas.height);
                        };
                    };

                    reader.readAsDataURL(file);
                }
            },
            saveImage () {
                var vm = this
                var dataURL = vm.canvas.toDataURL()
                var img = vm.$refs.img
                img.src = dataURL
            }
        }
    }
</script>

<style scoped>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
canvas {
    border: 1px solid red;
    cursor: crosshair;
}
</style>