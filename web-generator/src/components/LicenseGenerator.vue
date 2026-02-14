<template>
  <div class="generator">
    <h1>CSSM Unlimited License Generator</h1>
    <form @submit.prevent="generateLicense">
      <label>
        Version:
        <select v-model="input.version">
          <option value="2.0">2.0 (current)</option>
          <option value="1.0">1.0 (legacy)</option>
        </select>
      </label>
      <label>
        Year:
        <input v-model="input.year" type="text" placeholder="2026" required>
      </label>
      <label>
        Author (copyright holder):
        <input v-model="input.author" type="text" placeholder="Serhii Cherneha" required>
      </label>
      <label>
        Project Name (optional):
        <input v-model="input.project" type="text" placeholder="My Project">
      </label>
      <button type="submit">Generate</button>
    </form>
    <div v-if="licenseText" class="output">
      <h2>Generated LICENSE-{{input.version}}.txt</h2>
      <textarea readonly :value="licenseText" rows="16"></textarea>
      <div class="actions">
        <button @click="copyToClipboard">Copy to clipboard</button>
        <button @click="downloadFile">Download LICENSE-{{input.version}}.txt</button>
      </div>
      <small>
        For usage &amp; license details, see:
        <a href="https://github.com/Fyennyi/cssm-license-spec" target="_blank" rel="noopener">cssm-license-spec</a>
      </small>
    </div>
  </div>
</template>

<script>
const LICENSE_TEMPLATES = {
  "2.0": `CSSM Unlimited License v2.0
Copyright (c) [YEAR] [AUTHOR]
Updated November 9, 2024

[PROJECT]

[Insert full legal text of v2.0, replacing [YEAR], [AUTHOR], [PROJECT]]
`,
  "1.0": `CSSM Unlimited License v1.0
Copyright (c) [YEAR] [AUTHOR]
Updated December 15, 2019

[PROJECT]

[Insert full legal text of v1.0, replacing [YEAR], [AUTHOR], [PROJECT]]
`
};

export default {
  name: "LicenseGenerator",
  data() {
    return {
      input: {
        version: '2.0',
        year: new Date().getFullYear(),
        author: '',
        project: ''
      },
      licenseText: ''
    };
  },
  methods: {
    generateLicense() {
      let template = LICENSE_TEMPLATES[this.input.version];
      let license = template
        .replace(/\[YEAR\]/g, this.input.year)
        .replace(/\[AUTHOR\]/g, this.input.author || '[AUTHOR]')
        .replace(/\[PROJECT\]/g, this.input.project ? `Project: ${this.input.project}` : '');
      this.licenseText = license.trim();
    },
    copyToClipboard() {
      navigator.clipboard.writeText(this.licenseText).then(() => {
        alert('Copied to clipboard!');
      });
    },
    downloadFile() {
      const blob = new Blob([this.licenseText], { type: "text/plain" });
      const url = window.URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url;
      a.download = `LICENSE-${this.input.version}.txt`;
      a.click();
      window.URL.revokeObjectURL(url);
    }
  }
}
</script>

<style scoped>
.generator {
  max-width: 520px;
  margin: 2em auto;
  padding: 2em 2em 1em 2em;
  background: #fcfcfc;
  border-radius: 10px;
  box-shadow: 0px 2px 12px #0002;
  font-family: system-ui, sans-serif;
}
label {
  display: block;
  margin: 1em 0 .5em 0;
}
input, select, textarea {
  width: 100%;
  padding: .5em;
  font-size: 1em;
  margin-top: 3px;
}
button {
  margin: .7em .7em .7em 0;
  padding: .5em 1.2em;
  font-size: 1em;
  border: 0;
  border-radius: 5px;
  background: #536dfe;
  color: #fff;
  cursor: pointer;
  transition: background .2s;
}
button:hover {
  background: #304ffe;
}
.output {
  margin-top: 2em;
}
.actions {
  margin: 1em 0;
}
textarea[readonly] {
  background: #f2f2f2;
  color: #1a1a1a;
  border: 1px solid #ddd;
  font-family: monospace;
  resize: vertical;
}
small {
  display: block;
  margin-top: 1.5em;
  color: #444b;
}
</style>
