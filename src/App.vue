<script setup>
import { onMounted } from 'vue';
import * as pdfjsLib from 'pdfjs-dist';
const CMAP_URL =
  'https://cdn.jsdelivr.net/npm/pdfjs-dist@4.0.269/cmaps/';
const CMAP_PACKED = true;

onMounted(async () => {
  //
  // If absolute URL from the remote server is provided, configure the CORS
  // header on that server.
  //
  const url = '/ARTESSIMO STELLA_403号室.pdf';

  //
  // The workerSrc property shall be specified.
  //
  pdfjsLib.GlobalWorkerOptions.workerSrc =
    'https://cdn.jsdelivr.net/npm/pdfjs-dist@4.0.269/build/pdf.worker.min.mjs';

  //
  // Asynchronous download PDF
  //

  const loadingTask = pdfjsLib.getDocument({
    url,
    cMapUrl: CMAP_URL,
    cMapPacked: CMAP_PACKED
  });
  const pdf = await loadingTask.promise;

  //
  // Fetch the first page
  //
  const page = await pdf.getPage(1);
  const scale = 1.5;
  const viewport = page.getViewport({ scale });
  // Support HiDPI-screens.
  const outputScale = window.devicePixelRatio || 1;

  //
  // Prepare canvas using PDF page dimensions
  //
  const canvas = document.getElementById('the-canvas');
  const context = canvas.getContext('2d');

  canvas.width = Math.floor(viewport.width * outputScale);
  canvas.height = Math.floor(viewport.height * outputScale);
  canvas.style.width = Math.floor(viewport.width) + 'px';
  canvas.style.height = Math.floor(viewport.height) + 'px';

  const transform =
    outputScale !== 1 ? [outputScale, 0, 0, outputScale, 0, 0] : null;

  //
  // Render PDF page into canvas context
  //
  const renderContext = {
    canvasContext: context,
    transform,
    viewport
  };
  page.render(renderContext);
});
</script>

<template>
  <canvas
    id="the-canvas"
    style="border: 1px solid black; direction: ltr"
  ></canvas>
</template>