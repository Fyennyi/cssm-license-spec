<template>
  <div class="min-h-screen flex flex-col items-center justify-center p-6">
    <div class="w-full max-w-2xl">
      <div class="text-center mb-10">
        <div class="inline-flex items-center justify-center w-16 h-16 rounded-2xl bg-indigo-500/20 mb-4">
          <svg class="w-8 h-8 text-indigo-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
          </svg>
        </div>
        <h1 class="text-3xl font-bold text-white mb-2">CSSM Unlimited License</h1>
        <p class="text-slate-400">Generate license files for your projects</p>
      </div>

      <div class="bg-slate-800/50 rounded-2xl border border-slate-700/50 p-8 backdrop-blur-sm">
        <div v-if="loading" class="flex items-center justify-center py-12">
          <div class="flex items-center gap-3 text-slate-400">
            <svg class="w-5 h-5 animate-spin" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <span>Loading license templates...</span>
          </div>
        </div>

        <div v-else-if="error" class="flex flex-col items-center justify-center py-12 text-center">
          <div class="flex items-center gap-2 text-red-400 mb-4">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <span>{{ error }}</span>
          </div>
          <button @click="fetchLicenseTemplates" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-white text-sm rounded-lg transition-colors">
            Try Again
          </button>
        </div>

        <form v-else @submit.prevent="generateLicense" class="space-y-6">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div>
              <label class="block text-sm font-medium text-slate-300 mb-2">Version</label>
              <select v-model="input.version" class="w-full px-4 py-3 bg-slate-900/50 border border-slate-600 rounded-lg text-white focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition-all">
                <option value="2.0">v2.0 (current)</option>
                <option value="1.0">v1.0 (legacy)</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-slate-300 mb-2">Year</label>
              <input v-model="input.year" type="text" placeholder="2026" required class="w-full px-4 py-3 bg-slate-900/50 border border-slate-600 rounded-lg text-white placeholder-slate-500 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition-all">
            </div>
          </div>

          <div>
            <label class="block text-sm font-medium text-slate-300 mb-2">Author / Copyright Holder</label>
            <input v-model="input.author" type="text" placeholder="Your Name or Organization" required class="w-full px-4 py-3 bg-slate-900/50 border border-slate-600 rounded-lg text-white placeholder-slate-500 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition-all">
          </div>

          <button type="submit" class="w-full py-3 px-6 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold rounded-lg transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 focus:ring-offset-slate-800">
            Generate License
          </button>
        </form>

        <div v-if="licenseText" class="mt-8 pt-8 border-t border-slate-700/50">
          <div class="flex items-center justify-between mb-4">
            <h2 class="text-lg font-semibold text-white">Generated LICENSE-{{ input.version }}.txt</h2>
            <span class="text-xs text-slate-500">Ready to use</span>
          </div>
          
          <div class="relative">
            <textarea readonly :value="licenseText" rows="12" class="w-full p-4 bg-slate-900/80 border border-slate-600 rounded-lg text-slate-300 font-mono text-sm resize-y focus:outline-none"></textarea>
            <button @click="copyToClipboard" class="absolute top-2 right-2 p-2 bg-slate-700 hover:bg-slate-600 rounded-md text-slate-300 transition-colors" title="Copy to clipboard">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
              </svg>
            </button>
          </div>

          <div class="flex flex-wrap gap-3 mt-4">
            <button @click="copyToClipboard" class="flex items-center gap-2 px-4 py-2 bg-slate-700 hover:bg-slate-600 text-white text-sm font-medium rounded-lg transition-colors">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
              </svg>
              Copy
            </button>
            <button @click="downloadFile" class="flex items-center gap-2 px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white text-sm font-medium rounded-lg transition-colors">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              Download LICENSE-{{ input.version }}.txt
            </button>
          </div>
        </div>

        <div class="mt-8 pt-6 border-t border-slate-700/50">
          <p class="text-sm text-slate-500 text-center">
            For usage & license details, visit
            <a href="https://github.com/Fyennyi/cssm-license-spec" target="_blank" rel="noopener" class="text-indigo-400 hover:text-indigo-300 transition-colors">
              github.com/Fyennyi/cssm-license-spec
            </a>
          </p>
        </div>
      </div>

      <div class="mt-8 text-center">
        <p class="text-xs text-slate-600">Copyright &copy; {{ new Date().getFullYear() }} CSSM Group</p>
      </div>
    </div>
  </div>
</template>

<script>
const LICENSE_TEMPLATES = {
  "2.0": null,
  "1.0": null
};

export default {
  name: "LicenseGenerator",
  data() {
    return {
      input: {
        version: '2.0',
        year: new Date().getFullYear(),
        author: ''
      },
      licenseText: '',
      licenseTemplates: {
        '2.0': '',
        '1.0': ''
      },
      loading: true,
      error: null
    };
  },
  async mounted() {
    await this.fetchLicenseTemplates();
  },
  methods: {
    async fetchLicenseTemplates() {
      this.loading = true;
      this.error = null;
      try {
        const [v2, v1] = await Promise.all([
          fetch('https://raw.githubusercontent.com/Fyennyi/cssm-license-spec/main/LICENSE-2.0.txt'),
          fetch('https://raw.githubusercontent.com/Fyennyi/cssm-license-spec/main/LICENSE-1.0.txt')
        ]);
        if (!v2.ok || !v1.ok) throw new Error('Failed to fetch license templates');
        this.licenseTemplates['2.0'] = await v2.text();
        this.licenseTemplates['1.0'] = await v1.text();
      } catch (e) {
        this.error = 'Failed to load license templates. Please try again.';
        console.error(e);
      } finally {
        this.loading = false;
      }
    },
    generateLicense() {
      let template = this.licenseTemplates[this.input.version];
      if (!template) {
        this.error = 'License template not loaded';
        return;
      }
      
      let license = template
        .replace(/\[YEAR\]/g, this.input.year)
        .replace(/\[AUTHOR NAME\]/g, this.input.author || '[AUTHOR NAME]')
        .replace(/\[Copyright Holder\]/g, this.input.author || '[Copyright Holder]')
        .replace(/\[AUTHOR\]/g, this.input.author || '[AUTHOR]')
        .replace(/\[NAME\]/g, this.input.author || '[NAME]');
      
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
