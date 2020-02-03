<template>
<div :class="{
  'modal': true,
  'is-active': active
}">
  <div class="modal-background"></div>
  <div class="modal-content">

    <div class="box content">
      <h1 class="title">
        {{ $t("main.csv.preview_title") }}
      </h1>

      <div class="description">
        <div class="flex-item">
          <p>
            {{ $t("main.csv.preview_description") }}
          </p>
          <p>
            {{ $t("main.csv.preview_required") }}
          </p>
        </div>
        <div class="flex-item">
          <!--text-field
            ref="nameField"
            input-class="task-status-name"
            :label="$t('main.csv.preview_episode_name')"
            v-model="form.name"
            v-focus
            v-show="isTVShow"
          /-->
          <h2 class="legend-title">{{ $t('main.csv.legend') }}</h2>
          <dl class="legend">
            <dt></dt>
            <dd>{{ $t('main.csv.legend_ok') }}</dd>
            <dt class="ignored"></dt>
            <dd>{{ $t('main.csv.legend_ignored') }}</dd>
            <dt class="missing"></dt>
            <dd>{{ $t('main.csv.legend_missing') }}</dd>
          </dl>
        </div>
      </div>

      <div class="render-container">
        <table class="render">
          <colgroup>
            <col
              v-for="(cell, index) in parsedCsv[0]"
              :key="`col-${index}`"
              :class="stateColumn(cell, columns)"
            />
            <col
              v-for="item in missingColumns"
              :key="`col-missing-${item}`"
              class="missing"
            />
          </colgroup>
          <thead>
            <tr class="render-headers">
              <th
                v-for="(cell, index) in parsedCsv[0]"
                :key="`header-${index}`"
              >
              {{ cell || '-' }}
              </th>
              <th
                v-for="cell in missingColumns"
                :key="`header-${cell}`"
              >
              {{ cell }}
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(line, index) in startFrom(parsedCsv, 1)"
              :key="`line-${index}`"
            >
              <td
                v-for="(cell, index) in line"
                :key="`cell-${index}`"
              >
                {{ cell || '-' }}
              </td>
              <td
                v-for="cell in missingColumns"
                :key="`cell-${cell}`"
              >
                {{ '-' }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="render-footer">
        <button-simple
          :text="$t('main.csv.preview_reupload')"
          @click="onReupload"
        />
        <modal-footer
          :error-text="$t('main.csv.error_upload')"
          :is-loading="isLoading"
          :is-disabled="formData === undefined"
          :is-error="isError"
          @confirm="onConfirmClicked"
          @cancel="$emit('cancel')"
        />
      </div>
    </div>
  </div>
</div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import { modalMixin } from './base_modal'
// import TextField from '../widgets/TextField'
import ButtonSimple from '../widgets/ButtonSimple'
import ModalFooter from './ModalFooter'

export default {
  name: 'import-render-modal',
  mixins: [modalMixin],
  components: {
    ButtonSimple,
    // TextField,
    ModalFooter
  },

  data () {
    return {
      formData: null,
      form: {
        name: ''
      }
    }
  },

  props: {
    active: {
      type: Boolean,
      default: false
    },
    parsedCsv: {
      type: Array,
      default: () => []
    },
    columns: {
      type: Array,
      default: () => []
    },
    isLoading: {
      type: Boolean,
      default: false
    },
    isError: {
      type: Boolean,
      default: false
    }
  },

  mounted () {
    this.formData = null
  },

  computed: {
    ...mapGetters([
      'isTVShow'
    ]),
    missingColumns () {
      if (this.parsedCsv.length !== 0) {
        return this.columns.filter(item => !this.parsedCsv[0].includes(item))
      } else {
        return undefined
      }
    }
  },

  methods: {
    ...mapActions([
    ]),
    onConfirmClicked () {
      this.$emit('confirm')
    },
    onReupload () {
      this.$emit('reupload')
    },
    startFrom (arr, index) {
      return arr.slice(index)
    },
    stateColumn (data, list) {
      if (!list.includes(data)) {
        return 'ignored'
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.dark {
  .render-container {
    .render {
      th, td {
        border: 1px solid $dark-grey-lightest;
        color: $white;
      }
      tr:not(.render-headers):hover {
        background-color: $dark-grey-lightmore;
      }
    }
  }
  .legend-title {
    color: $white;
  }
  .legend {
    dt {
      border: 1px solid $dark-grey-lightest;
    }
  }
  .ignored {
    background-color: $dark-grey
  }
}
.modal-content {
  margin: 6rem auto 1.4rem;
  max-width: calc(100vw - 4rem);
  max-height: calc(100% - 6rem);
  width: auto;
}
.modal-content .box p.text {
  margin-bottom: 1em;
}
.error {
  margin-top: 1em;
}
.description {
  display: flex;
  margin-bottom: 1em;
  align-items: center;
  .flex-item {
    flex: 1 1 50%;
  }
}
.render-container {
  overflow: auto;
  .render-headers {
    .field {
      margin: 0;
    }
  }
  .render {
    width: 100%;
    border: 1px solid $light-grey-light;
    th, td {
      color: $dark-grey;
      border: 1px solid $light-grey-light;
      padding: .75rem;
    }
    tr:hover {
      background: none;
    }
    tr:not(.render-headers):hover {
      background-color: $white-grey-light;
    }
  }
}
.render-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 2rem;
}
.legend-title {
  font-size: 1.2rem;
}
.legend {
  display: flex;
  align-items: center;
  dt {
    display: inline-block;
    width: 1.5rem;
    height: 1.5rem;
    border: 1px solid $light-grey-light;
  }
  dd {
    margin: 0 1rem 0 .5rem;
  }
}
.ignored {
  background-color: rgba($light-grey-light, .6)
}
.missing {
  background-color: rgba($red, .6)
}
</style>