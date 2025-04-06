<template>
  <div class="min-h-screen bg-white text-gray-900 flex flex-col">
    <!-- Header -->
    <header class="bg-black text-white p-4">
      <div class="container mx-auto flex items-center justify-between">
        <h1 class="text-2xl font-bold">CSS Grid Generator</h1>
        <button @click="copyCSS" class="bg-white text-black px-4 py-2 rounded font-medium hover:bg-gray-200 transition">
          Copy CSS
        </button>
      </div>
    </header>

    <!-- Main Content -->
    <div class="container mx-auto flex flex-col md:flex-row flex-1 p-4 gap-6">
      <!-- Controls Panel -->
      <div class="w-full md:w-1/3 bg-gray-100 p-6 rounded shadow-md">
        <h2 class="text-xl font-bold mb-4 border-b border-gray-300 pb-2">Grid Settings</h2>

        <!-- Grid Template Columns -->
        <div class="mb-6">
          <h3 class="font-semibold mb-2">Columns</h3>
          <div class="flex items-center mb-2">
            <label class="w-32">Number of Columns:</label>
            <input
                type="number"
                v-model="columns"
                min="1"
                max="12"
                class="border border-gray-300 rounded p-2 w-20"
            />
          </div>
          <div class="mb-2">
            <label class="block mb-1">Column Sizing:</label>
            <div class="flex flex-wrap gap-2">
              <button
                  v-for="(column, index) in columnValues"
                  :key="index"
                  @click="editColumnValue(index)"
                  class="border border-gray-400 px-3 py-1 rounded text-sm flex-grow"
              >
                {{ column }}
              </button>
              <input
                  v-if="editingColumnIndex !== null"
                  ref="columnInput"
                  v-model="columnValues[editingColumnIndex]"
                  @blur="editingColumnIndex = null"
                  @keydown.enter="editingColumnIndex = null"
                  class="border border-black rounded p-1 w-full"
              />
            </div>
          </div>
        </div>

        <!-- Grid Template Rows -->
        <div class="mb-6">
          <h3 class="font-semibold mb-2">Rows</h3>
          <div class="flex items-center mb-2">
            <label class="w-32">Number of Rows:</label>
            <input
                type="number"
                v-model="rows"
                min="1"
                max="12"
                class="border border-gray-300 rounded p-2 w-20"
            />
          </div>
          <div class="mb-2">
            <label class="block mb-1">Row Sizing:</label>
            <div class="flex flex-wrap gap-2">
              <button
                  v-for="(row, index) in rowValues"
                  :key="index"
                  @click="editRowValue(index)"
                  class="border border-gray-400 px-3 py-1 rounded text-sm flex-grow"
              >
                {{ row }}
              </button>
              <input
                  v-if="editingRowIndex !== null"
                  ref="rowInput"
                  v-model="rowValues[editingRowIndex]"
                  @blur="editingRowIndex = null"
                  @keydown.enter="editingRowIndex = null"
                  class="border border-black rounded p-1 w-full"
              />
            </div>
          </div>
        </div>

        <!-- Grid Gap -->
        <div class="mb-6">
          <h3 class="font-semibold mb-2">Grid Gap</h3>
          <div class="flex items-center mb-2">
            <label class="w-32">Column Gap:</label>
            <input
                type="text"
                v-model="columnGap"
                class="border border-gray-300 rounded p-2 w-28"
                placeholder="10px"
            />
          </div>
          <div class="flex items-center mb-2">
            <label class="w-32">Row Gap:</label>
            <input
                type="text"
                v-model="rowGap"
                class="border border-gray-300 rounded p-2 w-28"
                placeholder="10px"
            />
          </div>
        </div>

        <!-- Preview Settings -->
        <div class="mb-6">
          <h3 class="font-semibold mb-2">Preview Settings</h3>
          <div class="flex items-center gap-4 mb-2">
            <label class="flex items-center gap-2 cursor-pointer">
              <input type="checkbox" v-model="showNumbers" class="form-checkbox h-4 w-4" />
              <span>Show Cell Numbers</span>
            </label>
          </div>
          <div class="flex items-center mb-2">
            <label class="w-32">Cell Color:</label>
            <input
                type="color"
                v-model="cellColor"
                class="border border-gray-300 rounded p-1 h-8 w-20"
            />
          </div>
          <div class="flex items-center mb-2">
            <label class="w-32">Preview Size:</label>
            <select v-model="previewSize" class="border border-gray-300 rounded p-2">
              <option value="small">Small</option>
              <option value="medium">Medium</option>
              <option value="large">Large</option>
              <option value="custom">Custom</option>
            </select>
          </div>
          <div v-if="previewSize === 'custom'" class="flex flex-col gap-2 mt-2">
            <div class="flex items-center">
              <label class="w-32">Width:</label>
              <input
                  type="text"
                  v-model="customPreviewWidth"
                  class="border border-gray-300 rounded p-2 w-28"
                  placeholder="600px"
              />
            </div>
            <div class="flex items-center">
              <label class="w-32">Height:</label>
              <input
                  type="text"
                  v-model="customPreviewHeight"
                  class="border border-gray-300 rounded p-2 w-28"
                  placeholder="400px"
              />
            </div>
          </div>
        </div>
      </div>

      <!-- Preview & Code Panel -->
      <div class="w-full md:w-2/3 flex flex-col gap-6">
        <!-- Grid Preview -->
        <div class="bg-gray-100 p-6 rounded shadow-md flex flex-col">
          <div class="flex justify-between items-center mb-4 border-b border-gray-300 pb-2">
            <h2 class="text-xl font-bold">Grid Preview</h2>
            <div class="flex items-center gap-2">
              <span class="text-sm">Mode:</span>
              <button
                  @click="previewMode = 'view'"
                  class="px-3 py-1 rounded text-sm"
                  :class="previewMode === 'view' ? 'bg-black text-white' : 'bg-gray-200'"
              >
                View
              </button>
              <button
                  @click="previewMode = 'edit'"
                  class="px-3 py-1 rounded text-sm"
                  :class="previewMode === 'edit' ? 'bg-black text-white' : 'bg-gray-200'"
              >
                Edit
              </button>
            </div>
          </div>

          <!-- Interactive Preview Instructions -->
          <div v-if="previewMode === 'edit'" class="mb-4 bg-gray-200 p-3 rounded text-sm">
            <p class="font-medium mb-1">Interactive Preview Mode:</p>
            <ul class="list-disc pl-5 space-y-1">
              <li>Drag column dividers horizontally to resize columns</li>
              <li>Drag row dividers vertically to resize rows</li>
              <li>Double-click on a column/row divider to edit its value precisely</li>
            </ul>
          </div>

          <div
              class="grid-preview-container border border-gray-400 bg-white overflow-auto relative"
              :style="previewContainerStyle"
          >
            <!-- Column Rulers (top) -->
            <div v-if="previewMode === 'edit'" class="column-rulers h-6 bg-gray-100 border-b border-gray-400 flex">
              <div
                  v-for="(column, index) in columnPositions"
                  :key="`col-ruler-${index}`"
                  class="h-full flex items-center justify-center text-xs"
                  :style="{ width: `${column.width}px`, borderRight: index < columnPositions.length - 1 ? '1px solid #cbd5e0' : 'none' }"
              >
                {{ column.value }}
              </div>
            </div>

            <!-- Row Rulers (left) -->
            <div v-if="previewMode === 'edit'" class="row-rulers w-10 bg-gray-100 border-r border-gray-400 absolute top-6 bottom-0 left-0 flex flex-col">
              <div
                  v-for="(row, index) in rowPositions"
                  :key="`row-ruler-${index}`"
                  class="w-full flex items-center justify-center text-xs"
                  :style="{ height: `${row.height}px`, borderBottom: index < rowPositions.length - 1 ? '1px solid #cbd5e0' : 'none' }"
              >
                {{ row.value }}
              </div>
            </div>

            <!-- Actual Grid Preview -->
            <div
                class="grid-preview"
                :class="{ 'pl-10 pt-6': previewMode === 'edit' }"
                :style="gridStyle"
            >
              <div
                  v-for="i in gridCellCount"
                  :key="i"
                  class="grid-cell border border-gray-300 flex items-center justify-center p-4 text-sm"
                  :style="{ backgroundColor: cellColor }"
              >
                <span v-if="showNumbers">{{ i }}</span>
              </div>

              <!-- Column Resize Handles -->
              <template v-if="previewMode === 'edit'">
                <div
                    v-for="(pos, index) in columnResizePositions"
                    :key="`col-resize-${index}`"
                    class="col-resize-handle absolute top-0 cursor-col-resize"
                    :style="{ left: `${pos}px`, height: '100%', width: '8px' }"
                    @mousedown="startColumnResize(index)"
                    @dblclick="openColumnEditor(index)"
                ></div>
              </template>

              <!-- Row Resize Handles -->
              <template v-if="previewMode === 'edit'">
                <div
                    v-for="(pos, index) in rowResizePositions"
                    :key="`row-resize-${index}`"
                    class="row-resize-handle absolute left-0 cursor-row-resize"
                    :style="{ top: `${pos}px`, width: '100%', height: '8px' }"
                    @mousedown="startRowResize(index)"
                    @dblclick="openRowEditor(index)"
                ></div>
              </template>
            </div>
          </div>

          <!-- Resize Value Editor Modal -->
          <div v-if="resizeEditor.show" class="resize-editor-modal fixed inset-0 flex items-center justify-center z-10">
            <div class="absolute inset-0 bg-black bg-opacity-50" @click="resizeEditor.show = false"></div>
            <div class="bg-white p-6 rounded shadow-lg z-20 w-80">
              <h3 class="text-lg font-bold mb-4">Edit {{ resizeEditor.type === 'column' ? 'Column' : 'Row' }} Size</h3>
              <div class="mb-4">
                <label class="block mb-2">Value:</label>
                <input
                    v-model="resizeEditor.value"
                    class="border border-gray-300 rounded p-2 w-full"
                    @keydown.enter="applyResizeValue"
                    ref="resizeEditorInput"
                />
                <p class="text-sm text-gray-600 mt-1">Examples: 1fr, 200px, 50%, auto, min-content</p>
              </div>
              <div class="flex justify-end gap-2">
                <button
                    @click="resizeEditor.show = false"
                    class="bg-gray-200 px-4 py-2 rounded"
                >
                  Cancel
                </button>
                <button
                    @click="applyResizeValue"
                    class="bg-black text-white px-4 py-2 rounded"
                >
                  Apply
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Generated CSS -->
        <div class="bg-gray-100 p-6 rounded shadow-md">
          <div class="flex justify-between items-center mb-4 border-b border-gray-300 pb-2">
            <h2 class="text-xl font-bold">Generated CSS</h2>
            <button @click="copyCSS" class="bg-black text-white px-4 py-2 rounded font-medium hover:bg-gray-800 transition">
              Copy CSS
            </button>
          </div>
          <pre class="bg-gray-900 text-white p-4 rounded overflow-auto text-sm" v-text="generatedCSS"></pre>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer class="bg-black text-white p-4 mt-6">
      <div class="container mx-auto text-center">
        <p>CSS Grid Generator | Built with Vue.js and Tailwind CSS</p>
      </div>
    </footer>

    <!-- Notification -->
    <div
        v-if="notification.show"
        class="fixed bottom-4 right-4 bg-black text-white p-4 rounded shadow-lg transition-opacity"
        :class="{ 'opacity-0': notification.fadeOut }"
    >
      {{ notification.message }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Grid settings
      columns: 3,
      rows: 3,
      columnValues: ['1fr', '1fr', '1fr'],
      rowValues: ['1fr', '1fr', '1fr'],
      columnGap: '10px',
      rowGap: '10px',

      // Preview settings
      previewMode: 'view', // 'view' or 'edit'
      previewSize: 'medium',
      customPreviewWidth: '600px',
      customPreviewHeight: '400px',

      // UI State
      editingColumnIndex: null,
      editingRowIndex: null,
      showNumbers: true,
      cellColor: '#f8f9fa',

      // Resize state
      resizing: {
        active: false,
        type: null, // 'column' or 'row'
        index: null,
        startPos: 0,
        startValue: '',
      },

      // Column and row positions for interactive editing
      columnPositions: [],
      rowPositions: [],

      // Resize editor modal
      resizeEditor: {
        show: false,
        type: null, // 'column' or 'row'
        index: null,
        value: '',
      },

      // Notification
      notification: {
        show: false,
        message: '',
        fadeOut: false
      }
    };
  },

  computed: {
    gridStyle() {
      return {
        display: 'grid',
        gridTemplateColumns: this.columnValues.slice(0, this.columns).join(' '),
        gridTemplateRows: this.rowValues.slice(0, this.rows).join(' '),
        columnGap: this.columnGap,
        rowGap: this.rowGap
      };
    },

    previewContainerStyle() {
      const sizeMap = {
        small: { width: '400px', height: '300px' },
        medium: { width: '600px', height: '400px' },
        large: { width: '800px', height: '500px' },
        custom: { width: this.customPreviewWidth, height: this.customPreviewHeight }
      };

      return {
        width: sizeMap[this.previewSize].width,
        height: sizeMap[this.previewSize].height
      };
    },

    gridCellCount() {
      return this.columns * this.rows;
    },

    generatedCSS() {
      return `.grid-container {
  display: grid;
  grid-template-columns: ${this.columnValues.slice(0, this.columns).join(' ')};
  grid-template-rows: ${this.rowValues.slice(0, this.rows).join(' ')};
  column-gap: ${this.columnGap};
  row-gap: ${this.rowGap};
}`;
    },

    // Calculate column resize handle positions
    columnResizePositions() {
      if (!this.columnPositions.length) return [];

      const positions = [];
      let currentPosition = 10; // Start after the row rulers width

      for (let i = 0; i < this.columnPositions.length - 1; i++) {
        currentPosition += this.columnPositions[i].width;
        positions.push(currentPosition);
      }

      return positions;
    },

    // Calculate row resize handle positions
    rowResizePositions() {
      if (!this.rowPositions.length) return [];

      const positions = [];
      let currentPosition = 6; // Start after the column rulers height

      for (let i = 0; i < this.rowPositions.length - 1; i++) {
        currentPosition += this.rowPositions[i].height;
        positions.push(currentPosition);
      }

      return positions;
    }
  },

  watch: {
    columns(newVal, oldVal) {
      if (newVal > oldVal) {
        // Add more columns if needed
        while (this.columnValues.length < newVal) {
          this.columnValues.push('1fr');
        }
      }
      this.updateGridMeasurements();
    },

    rows(newVal, oldVal) {
      if (newVal > oldVal) {
        // Add more rows if needed
        while (this.rowValues.length < newVal) {
          this.rowValues.push('1fr');
        }
      }
      this.updateGridMeasurements();
    },

    columnValues: {
      handler() {
        this.updateGridMeasurements();
      },
      deep: true
    },

    rowValues: {
      handler() {
        this.updateGridMeasurements();
      },
      deep: true
    },

    previewMode() {
      this.$nextTick(() => {
        this.updateGridMeasurements();
      });
    }
  },

  mounted() {
    this.updateGridMeasurements();

    // Add global event listeners for resize
    window.addEventListener('mousemove', this.handleResize);
    window.addEventListener('mouseup', this.endResize);

    // Update measurements when window resizes
    window.addEventListener('resize', this.updateGridMeasurements);
  },

  beforeUnmount() {
    // Clean up event listeners
    window.removeEventListener('mousemove', this.handleResize);
    window.removeEventListener('mouseup', this.endResize);
    window.removeEventListener('resize', this.updateGridMeasurements);
  },

  methods: {
    editColumnValue(index) {
      this.editingColumnIndex = index;
      this.$nextTick(() => {
        if (this.$refs.columnInput) {
          this.$refs.columnInput.focus();
        }
      });
    },

    editRowValue(index) {
      this.editingRowIndex = index;
      this.$nextTick(() => {
        if (this.$refs.rowInput) {
          this.$refs.rowInput.focus();
        }
      });
    },

    copyCSS() {
      navigator.clipboard.writeText(this.generatedCSS).then(() => {
        this.showNotification('CSS copied to clipboard!');
      }).catch(err => {
        this.showNotification('Failed to copy. Please try again.');
        console.error('Failed to copy: ', err);
      });
    },

    showNotification(message) {
      // Clear any existing notification
      clearTimeout(this.notificationTimeout);
      this.notification.fadeOut = false;
      this.notification.message = message;
      this.notification.show = true;

      // Start fadeout after 2 seconds
      this.notificationTimeout = setTimeout(() => {
        this.notification.fadeOut = true;

        // Hide completely after fade animation
        setTimeout(() => {
          this.notification.show = false;
        }, 300);
      }, 2000);
    },

    // Update measurements for the grid preview
    updateGridMeasurements() {
      this.$nextTick(() => {
        const gridPreview = document.querySelector('.grid-preview');
        if (!gridPreview) return;

        // Calculate column positions and widths
        const gridWidth = gridPreview.clientWidth;
        const totalColumnWidth = gridWidth - (parseInt(this.columnGap) * (this.columns - 1));

        // Parse column values and calculate actual widths
        this.columnPositions = this.calculateDimensions(
            this.columnValues.slice(0, this.columns),
            totalColumnWidth,
            'width'
        );

        // Calculate row positions and heights
        const gridHeight = gridPreview.clientHeight;
        const totalRowHeight = gridHeight - (parseInt(this.rowGap) * (this.rows - 1));

        // Parse row values and calculate actual heights
        this.rowPositions = this.calculateDimensions(
            this.rowValues.slice(0, this.rows),
            totalRowHeight,
            'height'
        );
      });
    },

    // Helper to calculate dimensions based on CSS values
    calculateDimensions(values, totalSize, dimension) {
      const results = [];

      // First pass: calculate fixed sizes and count fr units
      let totalFixed = 0;
      let totalFrUnits = 0;

      values.forEach(value => {
        if (value.endsWith('fr')) {
          totalFrUnits += parseFloat(value) || 1;
        } else if (value.endsWith('px')) {
          totalFixed += parseInt(value);
        } else if (value.endsWith('%')) {
          totalFixed += (parseInt(value) / 100) * totalSize;
        } else {
          // For auto, min-content, etc., assign a default size
          totalFixed += 100; // Default size for non-fr, non-fixed values
        }
      });

      // Calculate size per fr unit
      const remainingSize = Math.max(0, totalSize - totalFixed);
      const sizePerFr = totalFrUnits > 0 ? remainingSize / totalFrUnits : 0;

      // Second pass: assign actual sizes
      values.forEach(value => {
        let size;
        if (value.endsWith('fr')) {
          const fr = parseFloat(value) || 1;
          size = fr * sizePerFr;
        } else if (value.endsWith('px')) {
          size = parseInt(value);
        } else if (value.endsWith('%')) {
          size = (parseInt(value) / 100) * totalSize;
        } else {
          // For auto, min-content, etc.
          size = 100; // Default size
        }

        results.push({
          value,
          [dimension]: size
        });
      });

      return results;
    },

    // Start column resize
    startColumnResize(index) {
      this.resizing = {
        active: true,
        type: 'column',
        index,
        startPos: event.clientX,
        startValue: this.columnPositions[index].width,
        nextValue: this.columnPositions[index + 1].width
      };

      // Add a resize class to the body
      document.body.classList.add('resizing');
    },

    // Start row resize
    startRowResize(index) {
      this.resizing = {
        active: true,
        type: 'row',
        index,
        startPos: event.clientY,
        startValue: this.rowPositions[index].height,
        nextValue: this.rowPositions[index + 1].height
      };

      // Add a resize class to the body
      document.body.classList.add('resizing');
    },

    // Handle resize during mouse move
    handleResize(event) {
      if (!this.resizing.active) return;

      if (this.resizing.type === 'column') {
        // Calculate the difference from the start position
        const diff = event.clientX - this.resizing.startPos;

        // Update column widths
        const newWidth = Math.max(20, this.resizing.startValue + diff);
        const newNextWidth = Math.max(20, this.resizing.nextValue - diff);

        // Convert pixel measurements to fr units (approximate)
        const totalWidth = this.resizing.startValue + this.resizing.nextValue;
        const ratio = newWidth / totalWidth;

        // Update the column values
        const index = this.resizing.index;
        if (this.columnValues[index].endsWith('fr') && this.columnValues[index + 1].endsWith('fr')) {
          const totalFr = parseFloat(this.columnValues[index]) + parseFloat(this.columnValues[index + 1]);
          this.columnValues[index] = (totalFr * ratio).toFixed(2) + 'fr';
          this.columnValues[index + 1] = (totalFr * (1 - ratio)).toFixed(2) + 'fr';
        } else {
          // For non-fr units, convert to pixels
          this.columnValues[index] = Math.round(newWidth) + 'px';
          this.columnValues[index + 1] = Math.round(newNextWidth) + 'px';
        }
      } else if (this.resizing.type === 'row') {
        // Calculate the difference from the start position
        const diff = event.clientY - this.resizing.startPos;

        // Update row heights
        const newHeight = Math.max(20, this.resizing.startValue + diff);
        const newNextHeight = Math.max(20, this.resizing.nextValue - diff);

        // Convert pixel measurements to fr units (approximate)
        const totalHeight = this.resizing.startValue + this.resizing.nextValue;
        const ratio = newHeight / totalHeight;

        // Update the row values
        const index = this.resizing.index;
        if (this.rowValues[index].endsWith('fr') && this.rowValues[index + 1].endsWith('fr')) {
          const totalFr = parseFloat(this.rowValues[index]) + parseFloat(this.rowValues[index + 1]);
          this.rowValues[index] = (totalFr * ratio).toFixed(2) + 'fr';
          this.rowValues[index + 1] = (totalFr * (1 - ratio)).toFixed(2) + 'fr';
        } else {
          // For non-fr units, convert to pixels
          this.rowValues[index] = Math.round(newHeight) + 'px';
          this.rowValues[index + 1] = Math.round(newNextHeight) + 'px';
        }
      }
    },

    // End resize
    endResize() {
      if (!this.resizing.active) return;

      this.resizing.active = false;
      document.body.classList.remove('resizing');

      // Update the grid measurements after resize
      this.updateGridMeasurements();
    },

    // Open the editor to edit column value
    openColumnEditor(index) {
      this.resizeEditor = {
        show: true,
        type: 'column',
        index: index,
        value: this.columnValues[index]
      };

      this.$nextTick(() => {
        if (this.$refs.resizeEditorInput) {
          this.$refs.resizeEditorInput.focus();
        }
      });
    },

    // Open the editor to edit row value
    openRowEditor(index) {
      this.resizeEditor = {
        show: true,
        type: 'row',
        index: index,
        value: this.rowValues[index]
      };

      this.$nextTick(() => {
        if (this.$refs.resizeEditorInput) {
          this.$refs.resizeEditorInput.focus();
        }
      });
    },

    // Apply the resize value from the editor
    applyResizeValue() {
      if (this.resizeEditor.type === 'column') {
        this.columnValues[this.resizeEditor.index] = this.resizeEditor.value;
      } else if (this.resizeEditor.type === 'row') {
        this.rowValues[this.resizeEditor.index] = this.resizeEditor.value;
      }

      this.resizeEditor.show = false;
      this.updateGridMeasurements();
    }
  }
};
</script>

<style scoped>
.grid-preview-container {
  position: relative;
}

.grid-preview {
  min-height: 300px;
}

.grid-cell {
  transition: background-color 0.2s;
}

.col-resize-handle, .row-resize-handle {
  position: absolute;
  z-index: 10;
  background-color: transparent;
  transition: background-color 0.2s;
}

.col-resize-handle:hover {
  background-color: rgba(0, 0, 0, 0.2);
}

.row-resize-handle:hover {
  background-color: rgba(0, 0, 0, 0.2);
}

body.resizing {
  cursor: grabbing;
  user-select: none;
}

/* Fade animation for notification */
.transition-opacity {
  transition: opacity 0.3s ease;
}
.opacity-0 {
  opacity: 0;
}
</style>