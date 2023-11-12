<script>
  import { onMount } from 'svelte';
  import QRCode from 'qrcode';

  let vCardData = {
    firstName: '',
    lastName: '',
    email: '',
    street: '',
    number: '',
    postalCode: '',
    city: '',
    country: '',
    mobileNumber: '',
    workNumber: '',
    websiteURL: '',
  };

  let qrCodeSize = 200;

  onMount(() => {
    generateQRCode();
  });

  function generateQRCode() {
    const vCardString = createVCardString();
    const canvas = document.getElementById('qrcode');
    QRCode.toCanvas(canvas, vCardString, { width: qrCodeSize, height: qrCodeSize }, function (error) {
      if (error) console.error(error);
      console.log('QR Code generated!');
    });
  }

  function createVCardString() {
    const {
      firstName,
      lastName,
      email,
      street,
      number,
      postalCode,
      city,
      country,
      mobileNumber,
      workNumber,
      websiteURL,
    } = vCardData;

    let vCardString = 'BEGIN:VCARD\nVERSION:3.0';

    if (firstName || lastName) {
      vCardString += `\nN:${lastName || ''};${firstName || ''};;`;
      vCardString += `\nFN:${firstName || ''} ${lastName || ''}`;
    }

    if (mobileNumber) {
      vCardString += `\nTEL;TYPE=HOME,VOICE:${mobileNumber}`;
    }

    if (workNumber) {
      vCardString += `\nTEL;TYPE=WORK,VOICE:${workNumber}`;
    }

    if (street || number || postalCode || city || country) {
      vCardString += `\nADR;TYPE=HOME:;;${street || ''} ${number || ''};${city || ''};;${postalCode || ''};${country || ''}`;
    }

    if (email) {
      vCardString += `\nEMAIL;TYPE=PREF,INTERNET:${email}`;
    }

    if (websiteURL) {
      vCardString += `\nURL:${websiteURL}`;
    }

    vCardString += '\nEND:VCARD';

    return vCardString;
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
  <label>Vorname: <input bind:value={vCardData.firstName} on:input={generateQRCode} /></label>
  <label>Nachname: <input bind:value={vCardData.lastName} on:input={generateQRCode} /></label>
  <label>E-Mail: <input bind:value={vCardData.email} on:input={generateQRCode} /></label>
  <label>Straße: <input bind:value={vCardData.street} on:input={generateQRCode} /></label>
  <label>Nummer: <input bind:value={vCardData.number} on:input={generateQRCode} /></label>
  <label>PLZ: <input bind:value={vCardData.postalCode} on:input={generateQRCode} /></label>
  <label>Ort: <input bind:value={vCardData.city} on:input={generateQRCode} /></label>
  <label>Land: <input bind:value={vCardData.country} on:input={generateQRCode} /></label>
  <label>Handynummer: <input bind:value={vCardData.mobileNumber} on:input={generateQRCode} /></label>
  <label>Arbeitsnummer: <input bind:value={vCardData.workNumber} on:input={generateQRCode} /></label>
  <label>Webseiten URL: <input bind:value={vCardData.websiteURL} on:input={generateQRCode} /></label>
  <input type="number" bind:value={qrCodeSize} placeholder="Enter size" on:input={generateQRCode} />
  <canvas id="qrcode"></canvas>
  <button on:click={copyToClipboard}>Copy to Clipboard</button>
  <button on:click={downloadImage}>Download as PNG</button>
</div>
