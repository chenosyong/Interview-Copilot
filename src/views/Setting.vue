<template>
  <div>

    <div class="desc_text">
      The following settings are only retained locally in your browser.
      See <a :href="github_url" target="_blank">Github</a> for setup instructions.
    </div>

    <h1>AI Provider Settings</h1>
    <div class="desc_text">Select your AI provider and enter the corresponding API Key</div>

    <div class="separator">
      <el-select v-model="ai_provider" placeholder="Select AI Provider" @change="onProviderChange">
        <el-option label="DeepSeek" value="deepseek"></el-option>
        <el-option label="Google Gemini" value="gemini"></el-option>
        <el-option label="OpenAI" value="openai"></el-option>
        <el-option label="KIMI" value="kimi"></el-option>
      </el-select>
    </div>

    <div v-if="ai_provider === 'openai'">
      <el-input placeholder="sk-xxxx" v-model="openai_key" @change="onKeyChange('openai_key')">
        <template slot="prepend">OpenAI Key:</template>
      </el-input>
      <div class="separator">
        GPT Model:
        <el-radio-group v-model="gpt_model" @change="onKeyChange('gpt_model')">
          <el-radio label="gpt-3.5-turbo"></el-radio>
          <el-radio label="gpt-4"></el-radio>
        </el-radio-group>
      </div>
    </div>

    <div v-if="ai_provider === 'deepseek'">
      <el-input placeholder="DeepSeek API Key" v-model="deepseek_key" @change="onKeyChange('deepseek_key')">
        <template slot="prepend">DeepSeek Key:</template>
      </el-input>
    </div>

    <div v-if="ai_provider === 'gemini'">
      <el-input placeholder="Gemini API Key" v-model="gemini_key" @change="onKeyChange('gemini_key')">
        <template slot="prepend">Gemini Key:</template>
      </el-input>
    </div>

    <div v-if="ai_provider === 'kimi'">
      <el-input placeholder="KIMI API Key" v-model="kimi_key" @change="onKeyChange('kimi_key')">
        <template slot="prepend">KIMI Key:</template>
      </el-input>
    </div>

    <div class="separator">
      <div class="desc_text">GPT Prompt:</div>
      <el-input type="textarea" placeholder="You can setup custom prompt here" :rows="5"
                v-model="gpt_system_prompt" @change="onKeyChange('gpt_system_prompt')"/>
    </div>


    <h1>Azure Speech Recognition</h1>
    <div class="desc_text">
      We use Microsoft Azure's speech recognition service. You can apply for a free Azure token by referring to <a
        :href="azure_application_url" target="_blank">this tutorial</a>:
    </div>
    <el-input placeholder="Input Your Azure Speech Resource Token (KEY 1)" v-model="azure_token"
              @change="onKeyChange('azure_token')">
      <template slot="prepend">Azure token:</template>
    </el-input>
    <div class="separator"></div>
    <el-input placeholder="e.g. eastasia" v-model="azure_region" @change="onKeyChange('azure_region')">
      <template slot="prepend">Location/Region</template>
    </el-input>
    <div class="separator"></div>
    <el-input placeholder="e.g. en-US" v-model="azure_language" @change="onKeyChange('azure_language')">
      <template slot="prepend">Recognition Language</template>
    </el-input>

    <div class="desc_text">
      <span style="text-decoration: gray">zh-CN</span> for Chinese, See <a :href="full_language_codec_url"
                                                                           target="_blank">here</a> for
      other language codes
    </div>

    <h1>Interview Settings</h1>
    <div class="separator">
      <el-input placeholder="Enter Job Position" v-model="job_position" @change="onKeyChange('job_position')">
        <template slot="prepend">Job Position:</template>
      </el-input>
    </div>

    <div class="separator">
      <el-upload
        class="upload-demo"
        action=""
        :on-change="handleResumeUpload"
        :auto-upload="false"
        :limit="1"
        :show-file-list="false">
        <el-button type="primary">Upload Resume</el-button>
        <div slot="tip" class="desc_text">Upload your resume (PDF or TXT)</div>
      </el-upload>
    </div>

<!--    <div>-->
<!--      <el-button @click="toDef">set all setting to default</el-button>-->
<!--    </div>-->

  </div>
</template>

<script>
import config_util from "../utils/config_util"

export default {
  name: 'HelloWorld',
  props: {},
  data() {
    return {
      ai_provider: "openai",
      openai_key: "",
      deepseek_key: "",
      gemini_key: "",
      kimi_key: "",
      gpt_model: "gpt-3.5-turbo",
      gpt_system_prompt: "",
      azure_token: "",
      azure_region: "",
      azure_language: "",
      open_ai_api_url: "https://platform.openai.com/api-keys",
      github_url: "https://github.com/interview-copilot/Interview-Copilot",
      azure_application_url: "https://github.com/interview-copilot/Interview-Copilot/blob/main/docs/azure_speech_service_tutorial.md",
      full_language_codec_url: "https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-support?tabs=stt#speech-to-text",
      job_position: "",
      resume_file: null
    }
  },
  mounted() {
    this.ai_provider = localStorage.getItem("ai_provider") || "openai";
    this.openai_key = localStorage.getItem("openai_key");
    this.deepseek_key = localStorage.getItem("deepseek_key");
    this.gemini_key = localStorage.getItem("gemini_key");
    this.kimi_key = localStorage.getItem("kimi_key");
    this.gpt_system_prompt = config_util.gpt_system_prompt();
    this.gpt_model = config_util.gpt_model();
    this.azure_token = localStorage.getItem("azure_token");
    this.azure_region = config_util.azure_region();
    this.azure_language = config_util.azure_language();
    this.job_position = localStorage.getItem("job_position") || "";
  },
  methods: {
    onKeyChange(key_name) {
      console.log("setItem", key_name, this[key_name]);
      localStorage.setItem(key_name, this[key_name]);
    },
    onProviderChange() {
      localStorage.setItem("ai_provider", this.ai_provider);
    },
    handleResumeUpload(file) {
      const reader = new FileReader();
      reader.onload = (e) => {
        this.resume_file = e.target.result;
        localStorage.setItem("resume_file", this.resume_file);
      };
      reader.readAsText(file.raw);
    },
    toDef() {
      localStorage.clear();
    }
  }


}


</script>
<style scoped>

.separator {
  margin-top: 10px;
}

.desc_text {
  color: gray;
  font-size: small;
  margin-bottom: 3px;
}

</style>
