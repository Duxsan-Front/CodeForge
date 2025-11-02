<template>
  <section class="code-section">
    <div class="code-header">
      <h3>Редактор кода</h3>
      <div class="code-actions">
        <button class="btn btn--small" @click="$emit('reset')">Сбросить</button>
      </div>
    </div>
    <div class="code-editor-container">
      <div class="code-editor-wrapper">
        <textarea 
          :value="code"
          @input="$emit('update:code', $event.target.value)"
          class="simple-editor"
          placeholder="Введите ваш код здесь..."
          spellcheck="false"
        ></textarea>
        <div class="line-numbers">
          <span v-for="n in lineCount" :key="n">{{ n }}</span>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'CodeSection',
  props: {
    code: String,
    lineCount: Number
  },
  emits: ['update:code', 'reset']
}
</script>

<style scoped>
.code-section {
  background: #303030;
  border-radius: 16px;
  padding: 1.25rem;
  border: 1px solid #404040;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.35);
  margin: 0;
}

.code-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.code-header h3 {
  margin: 0;
  color: #F8FAFC;
  font-size: 1.1rem;
  font-weight: 600;
}

.code-actions {
  display: flex;
  gap: 0.5rem;
}

.btn {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.7rem 1.1rem;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.15s ease;
  flex: 1;
  min-width: 120px;
  position: relative;
  overflow: hidden;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  transform: none !important;
}

.btn:active:not(:disabled) {
  transform: translateY(2px);
}

.btn--small {
  padding: 0.45rem 0.9rem;
  font-size: 0.7rem;
  border-radius: 8px;
  flex: none;
}

.btn--small:active:not(:disabled) {
  transform: translateY(1px);
}

.code-editor-container {
  position: relative;
  background: #1E1E1E;
  border-radius: 12px;
  border: 1px solid #404040;
  height: 400px;
  font-family: 'Monaco', 'Consolas', monospace;
  display: flex;
  flex-direction: column;
}

.code-editor-wrapper {
  position: relative;
  flex: 1;
  display: flex;
}

.simple-editor {
  flex: 1;
  background: transparent;
  border: none;
  color: #E2E8F0;
  padding: 0;
  margin: 0;
  font-family: inherit;
  font-size: 14px;
  line-height: 1.5;
  resize: none;
  outline: none;
  tab-size: 2;
  white-space: pre;
  overflow-wrap: normal;
  overflow: auto;
  width: 100%;
  height: 100%;
  min-height: 0;
  max-height: none;
  box-sizing: border-box;
  padding-left: 3rem;
}

.simple-editor::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

.simple-editor::-webkit-scrollbar-track {
  background: #1E1E1E;
  border-radius: 4px;
}

.simple-editor::-webkit-scrollbar-thumb {
  background: #3B82F6;
  border-radius: 4px;
}

.line-numbers {
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 3rem;
  background: #252526;
  border-right: 1px solid #404040;
  padding: 0.5rem 0;
  font-size: 12px;
  color: #858585;
  text-align: right;
  line-height: 1.5;
  user-select: none;
  font-family: 'Monaco', 'Consolas', monospace;
  z-index: 1;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.line-numbers span {
  padding-right: 0.5rem;
  width: 100%;
}

@media (max-width: 1024px) {
  .code-editor-container {
    height: 300px;
  }
}

@media (max-width: 768px) {
  .code-section {
    padding: 1rem;
    border-radius: 12px;
  }
  
  .code-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 0.5rem;
  }
  
  .code-actions {
    align-self: flex-end;
  }
  
  .code-editor-container {
    height: 300px;
  }
}

@media (max-width: 480px) {
  .code-section {
    padding: 0.75rem;
  }
  
  .code-editor-container {
    height: 250px;
  }
}
</style>