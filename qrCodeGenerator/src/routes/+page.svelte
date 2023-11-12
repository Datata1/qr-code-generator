<script>
  import { onMount } from 'svelte';
  import QRCode from 'qrcode';

  let qrCodeData = 'Hello, Svelte!';
  let qrCodeSize = 200;

  onMount(() => {
    generateQRCode();
  });

  function generateQRCode() {
    const canvas = document.getElementById('qrcode');
    QRCode.toCanvas(canvas, qrCodeData, { width: qrCodeSize, height: qrCodeSize }, function (error) {
      if (error) console.error(error);
      console.log('QR Code generated!');
    });
  }

  function copyToClipboard() {
    const canvas = document.getElementById('qrcode');
    canvas.toBlob((blob) => {
      const item = new ClipboardItem({ "image/png": blob });
      navigator.clipboard.write([item]).then(() => {
        console.log('QR Code copied to clipboard!');
      }).catch((error) => {
        console.error('Error copying to clipboard:', error);
      });
    });
  }

  function downloadImage() {
    const canvas = document.getElementById('qrcode');
    const dataUrl = canvas.toDataURL("image/png");

    const a = document.createElement('a');
    a.href = dataUrl;
    a.download = 'qrcode.png';
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
  }
</script>

<style>
  /* Customize styles if needed */
</style>

<div>
  <input bind:value={qrCodeData} placeholder="Enter data" on:input={generateQRCode} />
  <input type="number" bind:value={qrCodeSize} placeholder="Enter size" on:input={generateQRCode} />
  <canvas id="qrcode"></canvas>
  <button on:click={copyToClipboard}>Copy to Clipboard</button>
  <button on:click={downloadImage}>Download as PNG</button>
</div>
