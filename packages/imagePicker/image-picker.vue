<template>
  <div :class="`${prefixCls}-list`">
    <div :class="`${prefixCls}-flexbox`" v-for="(file, index) in files">
      <div :class="`${prefixCls}-item`"
           @click.self="onClick(index)"
           :style="{ backgroundImage: `url(${file.url})` }">
        <div :class="`${prefixCls}-item-remove`"
             v-if="removeable"
             @click="removeImage(index)">
          <v-icon type="cross-circle-o" color="#f00" size="xs"></v-icon>
        </div>
      </div>
    </div>
    <div :class="`${prefixCls}-flexbox`" v-if="selectable">
      <div :class="`${prefixCls}-item ${prefixCls}-upload-btn`">
        <input type="file" accept="image/*" @change="uploadHandle" v-if="multiple" multiple>
        <input type="file" accept="image/*" @change="uploadHandle" v-else="multiple">
      </div>
    </div>
  </div>
</template>

<script>
  import Icon from '../icon';

  const prefixCls = 'vm-image-picker';

  export default {
    name: 'VImagePicker',
    components: {
      [Icon.name]: Icon
    },
    props: {
      files: Array,
      multiple: {
        type: Boolean,
        default: false
      },
      selectable: {
        type: Boolean,
        default: true
      },
      removeable: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        prefixCls: prefixCls
      };
    },
    methods: {
      // 移除图片
      removeImage(index) {
        this.files.splice(index, 1);
        this.$emit('change', this.files, 'remove', index);
      },
      // 回显图片
      uploadHandle(e) {
        try {
          const files = Array.from(e.target.files);
          files.forEach((file) => {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => {
              this.files.push({
                url: reader.result
              });
            };
          });
          this.$emit('change', this.files, 'append');
        } catch (e) {
          this.$emit('fail', e);
        }
      },
      // 触发图片点击事件
      onClick(index) {
        this.$emit('image-click', index);
      }
    }
  };
</script>
