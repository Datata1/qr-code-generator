<script>
  import { onMount } from 'svelte';
  import QRCode from 'qrcode';

  let qrCodeData = '';
  let qrCodeSize = 200;

  onMount(() => {
    generateQRCode();
  });

  function generateQRCode() {
    const canvas = document.getElementById('qrcode');
    QRCode.toCanvas(canvas, qrCodeData, {errorCorrectionLevel: 'S', width: qrCodeSize, height: qrCodeSize }, function (error) {
      if (error) console.error(error);
      console.log('QR Code generated!');
    });
  }

  function handleFileUpload(event) {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = () => {
        qrCodeData = reader.result;
        generateQRCode();
      };
      reader.readAsText(file);
    }
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
  <input type="file" accept=".ics" on:change={handleFileUpload} />
  <input type="number" bind:value={qrCodeSize} placeholder="Enter size" on:input={generateQRCode} />
  <canvas id="qrcode"></canvas>
  <button on:click={copyToClipboard}>Copy to Clipboard</button>
  <button on:click={downloadImage}>Download as PNG</button>
</div>
