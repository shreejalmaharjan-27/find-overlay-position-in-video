<template>
  <p>Find position in video</p>
  <p>A simple web app to find the position of an overlayed image in viddeo.</p>
  <div>
    <label for="video">Choose Video</label>
    <input name="video" type="file" accept="video/*" @change="onVideoChange" />

    <label for="image">Choose Image</label>
    <input name="image" type="file" accept="image/*" @change="onImageChange" />

    <div style="position: relative">
      <video ref="videoRef" controls></video>
      <div
        id="imageContainer"
        ref="imageRef"
        style="width: 180px; height: 80px; resize: both"
        draggable="true"
      >
        <img
          :src="image"
          style="
            width: 100%;
            height: 100%;
            object-fit: contain;
            background-color: #ffffff2e;
          "
        />
      </div>
    </div>

    <div>
      <div style="margin-top: 10rem; display: flex; flex-direction: column">
        <span>X Position: {{ xPos }}</span>
        <span>Y Position: {{ yPos }}</span>
      </div>
    </div>

    <p>
      <a
        href="https://github.com/shreejalmaharjan-27/find-overlay-position-in-video"
        >Github</a
      >
    </p>
  </div>
</template>

<style scoped>
#imageContainer {
  position: absolute;
  z-index: 9;
  cursor: move;
}
</style>

<script setup lang="ts">
const video = ref(null);
const image = ref(null);
const videoRef = ref(null);
const imageRef = ref(null);

const xPos = ref(0);
const yPos = ref(0);

useHead({
  title: "Find position in video",
});

const onVideoChange = (e: any) => {
  const file = e.target.files[0];
  const reader = new FileReader();
  reader.onload = function (event) {
    const arrayBuffer = event.target.result;
    const blob = new Blob([arrayBuffer], { type: file.type });
    videoRef.value.src = URL.createObjectURL(blob);
  };
  reader.readAsArrayBuffer(file);
};

const onImageChange = (e: any) => {
  const file = e.target.files[0];
  const url = URL.createObjectURL(file);
  image.value = url;
};
onMounted(() => {
  dragElement(document.getElementById("imageContainer"));
});

function dragElement(elmnt) {
  var pos1 = 0,
    pos2 = 0,
    pos3 = 0,
    pos4 = 0;
  // otherwise, move the DIV from anywhere inside the DIV:
  elmnt.onmousedown = dragMouseDown;

  function dragMouseDown(e) {
    e = e || window.event;
    e.preventDefault();
    // get the mouse cursor position at startup:
    pos3 = e.clientX;
    pos4 = e.clientY;
    document.onmouseup = closeDragElement;
    // call a function whenever the cursor moves:
    document.onmousemove = elementDrag;
  }

  function elementDrag(e) {
    e = e || window.event;
    e.preventDefault();
    // calculate the new cursor position:
    pos1 = pos3 - e.clientX;
    pos2 = pos4 - e.clientY;
    pos3 = e.clientX;
    pos4 = e.clientY;
    // set the element's new position:
    elmnt.style.top = elmnt.offsetTop - pos2 + "px";
    elmnt.style.left = elmnt.offsetLeft - pos1 + "px";

    xPos.value = elmnt.offsetLeft - pos1;
    yPos.value = elmnt.offsetTop - pos2;
  }

  function closeDragElement() {
    // stop moving when mouse button is released:
    document.onmouseup = null;
    document.onmousemove = null;
  }
}
</script>
