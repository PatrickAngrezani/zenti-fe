
<script setup>
import { ref } from "vue";

const API_URL = import.meta.env.VITE_API_URL;

const topic = ref(
  "Chargebacks: Why They Happen and How Merchants Can Reduce Them"
);

const sourceUrl = ref(
  "https://docs.stripe.com/disputes?locale=pt-BR"
);

const sourceText = ref("");

const loading = ref(false);
const error = ref("");
const result = ref("");

async function generateContent() {
  error.value = "";
  result.value = "";

  if (!topic.value.trim() ||
      !sourceUrl.value.trim() ||
      !sourceText.value.trim()) {
    error.value = "Please complete all fields.";
    return;
  }

  loading.value = true;

  try {
    const response = await fetch(
      `${API_URL}/api/generate-content`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          topic: topic.value,
          sourceUrl: sourceUrl.value,
          sourceText: sourceText.value
        })
      }
    );

    const data = await response.json();

    if (!response.ok) {
      throw new Error(
        data.error || "Failed to generate content."
      );
    }

    result.value = data.content;
  } catch (err) {
    error.value =
      err.message || "Unable to connect to the backend.";
  } finally {
    loading.value = false;
  }
}
</script>

<template>
  <main class="container">
    <header>
      <span class="eyebrow">AI CONTENT WORKFLOW</span>
      <p>
        Turn reliable payment industry sources
        into educational content.
      </p>
    </header>

    <section class="panel">
      <form @submit.prevent="generateContent">
        <label for="topic">Content topic</label>
        <input
          id="topic"
          v-model="topic"
          required
          maxlength="300"
        />

        <label for="sourceUrl">Source URL</label>
        <input
          id="sourceUrl"
          v-model="sourceUrl"
          type="url"
          required
          maxlength="2000"
        />

        <label for="sourceText">Source material</label>
        <textarea
          id="sourceText"
          v-model="sourceText"
          required
          maxlength="20000"
          rows="9"
          placeholder="Paste your verified reference material here..."
        />

        <p class="hint">
          Use reliable source material. Review generated
          claims before publishing.
        </p>

        <button type="submit" :disabled="loading">
          {{ loading ? "Generating content..." :
             "Generate educational content" }}
        </button>
      </form>

      <p v-if="error" class="error" role="alert">
        {{ error }}
      </p>
    </section>

    <section v-if="loading" class="status" role="status">
      <p>AI is preparing your educational draft...</p>
    </section>

    <section v-if="result" class="panel result">
      <div class="result-header">
        <div>
          <span class="eyebrow">GENERATED OUTPUT</span>
        </div>
      </div>

      <pre>{{ result }}</pre>

      <p class="review">
        Human review required: verify factual claims,
        source accuracy, tone and CTA before publication.
      </p>

      <button
        type="button"
        class="secondary"
        @click="navigator.clipboard.writeText(result)"
      >
        Copy draft
      </button>
    </section>

    <footer>
      Zenti Content Engine · Proof of Concept
    </footer>
  </main>
</template>

<script>
export default {
  computed: {
    navigator() {
      return navigator;
    }
  }
};
</script>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #f5f7fb;
  color: #172033;
  font-family: Inter, Arial, sans-serif;
}

.container {
  max-width: 850px;
  margin: 0 auto;
  padding: 48px 20px;
}

header {
  margin-bottom: 28px;
}

.eyebrow {
  color: #4368c7;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 1.4px;
}

h1 {
  font-size: clamp(28px, 5vw, 40px);
  margin: 12px 0;
  letter-spacing: -1px;
}

header p {
  color: #667085;
  line-height: 1.6;
}

.panel {
  background: white;
  border: 1px solid #e5e9f2;
  border-radius: 14px;
  padding: 26px;
  box-shadow: 0 8px 30px #17203308;
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

label {
  font-size: 13px;
  font-weight: 700;
  margin-top: 12px;
}

input, textarea {
  width: 100%;
  padding: 12px 14px;
  border: 1px solid #d9dfeb;
  border-radius: 8px;
  font: inherit;
  font-size: 14px;
  background: white;
  color: #172033;
}

textarea {
  resize: vertical;
  line-height: 1.6;
}

input:focus, textarea:focus {
  outline: 2px solid #8ba7ff;
  outline-offset: 1px;
}

.hint, .review {
  color: #667085;
  font-size: 12px;
  line-height: 1.6;
}

button {
  border: 0;
  border-radius: 8px;
  padding: 14px;
  background: #3159c9;
  color: white;
  font-size: 14px;
  font-weight: 700;
  cursor: pointer;
  margin-top: 12px;
}

button:disabled {
  opacity: 0.6;
  cursor: wait;
}

button.secondary {
  background: #edf1fb;
  color: #3159c9;
}

.error {
  color: #b42318;
  font-size: 14px;
  margin-top: 18px;
}

.status {
  color: #4368c7;
  margin: 20px 0;
}

.result {
  margin-top: 24px;
}

.result-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
}

h2 {
  margin-top: 8px;
}

.tag {
  background: #fff3d6;
  color: #855b00;
  padding: 7px 10px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.result pre {
  white-space: pre-wrap;
  overflow-wrap: anywhere;
  font-family: inherit;
  font-size: 14px;
  line-height: 1.8;
  color: #344054;
  margin-top: 20px;
}

footer {
  text-align: center;
  color: #98a2b3;
  font-size: 12px;
  margin-top: 30px;
}

@media (max-width: 600px) {
  .container {
    padding: 28px 14px;
  }

  .panel {
    padding: 18px;
  }
}
</style>
