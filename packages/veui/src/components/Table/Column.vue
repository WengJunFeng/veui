<script>
import { uniqueId, pick } from 'lodash'
import table from '../../mixins/table'
import { getIndexOfType } from '../../utils/context'
import '../../common/uiTypes'

export default {
  name: 'veui-table-column',
  uiTypes: ['table-column'],
  mixins: [table],
  props: {
    title: String,
    field: {
      type: String,
      required: true
    },
    width: [String, Number],
    sortable: Boolean,
    align: {
      type: String,
      validator (val) {
        return !val || val === 'left' || val === 'right' || val === 'center'
      }
    },
    span: Function
  },
  data () {
    return {
      id: uniqueId('veui-table-column-')
    }
  },
  created () {
    let index = getIndexOfType(this, 'table-column')

    const props = ['title', 'field', 'sortable', 'width', 'align', 'span']

    let renderBody = item => {
      let defaultRow = this.$scopedSlots.default
      if (defaultRow) {
        return defaultRow(item)
      }
      return item[this.field]
    }

    this.table.add({
      ...pick(this, ...props, 'id'),
      index,
      hasFoot: () => {
        return !!this.$slots.foot
      },
      renderBody,
      renderSubRow: item => {
        let expandRow = this.$scopedSlots['sub-row']
        if (expandRow) {
          return expandRow(item)
        }
        return renderBody(item)
      },
      renderHead: () => {
        return this.$slots.head || this.title
      },
      renderFoot: () => {
        return this.$slots.foot || null
      }
    })
  },
  destroyed () {
    this.table.removeById(this.id)
  },
  render () {
    return null
  }
}
</script>
