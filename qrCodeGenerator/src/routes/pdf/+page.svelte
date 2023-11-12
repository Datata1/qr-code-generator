<script>
  import { onMount } from 'svelte';
  import QRCode from 'qrcode';

  let selectedPdf = ''; // Hier wird der Dateipfad des ausgewählten PDFs gespeichert
  let qrCodeData = ''; // Hier wird der Pfad zur PDF-Datei gespeichert
  let qrCodeSize = 200;

  const pdfOptions = [
    // Liste der Dateipfade im public/pdf-Ordner
    'pdf/file1.pdf',
    'pdf/file2.pdf',
    // Füge weitere Dateipfade hinzu, wenn benötigt
  ];

  onMount(() => {
    generateQRCodeData();
  });

  function generateQRCode() {
    const canvas = document.getElementById('qrcode');
    QRCode.toCanvas(canvas, qrCodeData, { width: qrCodeSize, height: qrCodeSize }, function (error) {
      if (error) {
        console.error('Error generating QR Code:', error);
      } else {
        console.log('QR Code generated!');
      }
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

  // Funktion zum Generieren der QR-Code-Daten (Pfad zur PDF-Datei)
  function generateQRCodeData() {
    // Stelle sicher, dass ein PDF ausgewählt ist
    if (selectedPdf) {
      // Erstelle den Pfad zur PDF-Datei im public/pdf-Ordner
      qrCodeData = `./${selectedPdf}`;
      generateQRCode();
    } else {
      console.error('Please select a PDF file.');
    }
  }
</script>

<style>
  /* Customize styles if needed */
</style>

<div>
  <label>Select PDF: 
    <select bind:value={selectedPdf} on:change={generateQRCodeData}>
      <option value="" disabled>Select PDF</option>
      {#each pdfOptions as pdf (pdf)}
        <option value={pdf}>{pdf}</option>
      {/each}
    </select>
  </label>
  <input type="number" bind:value={qrCodeSize} placeholder="Enter size" on:input={generateQRCode} />
  <canvas id="qrcode"></canvas>
  <button on:click={copyToClipboard}>Copy to Clipboard</button>
  <button on:click={downloadImage}>Download as PNG</button>
</div>
