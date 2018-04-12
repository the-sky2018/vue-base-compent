<template>
  <transition>
    <div class="m-window" tabindex="1">
      <header class="window-header" @mousedown="_headerMouseDown">
        <span class="title"></span>
        <button class="icon closeBtn" @click="_onCloseButtonClick"></button>
      </header>
      <section class="window-body">
      	内容组件
        <router-view></router-view>
      </section>
      <footer class="window-footer"></footer>
    </div>
  </transition>
</template>
<script>
var ButtonAlign = {
  'left': 'text-left',
  'right': 'text-right',
  'center': 'text-center'
}

var DEFAULTS = {

  bodyOverflowHidden: false,

  enableMobileHook: false,

  disableAutoFocusWhenClose: false,

  allowFormControlDisable: true,

  parent: document.body

};
export default {
  name: 'MDWindowView',
  props: {
    width: {
    	default: 600
    },
    height: {
    	default: 'auto'
	},
    title: {
    	default: '这是一个window弹框'
	},
    showFooter: {
    	default: true
	},
    onClose: {
    	default: null
	},
    data: {
    	// default: {}
	},
    buttons: {
    	// default: []
	},
    modal: {
    	default: false
	},
    enableESC: {
    	default: true
	},
    enableMove: {
    	default: true
	},
    onShow: {
    	default: _.noop
	},
    onHide: {
    	default: _.noop
	},
    top: {
    	default: 'auto'
	},
    enableMobileHook: {
    	default: undefined
	}
  },
  data: function() {
    return {
      $winLayoutWrapper: null,
      $header: null,
      $body: null,
      $footer: null,
      $title: null,
      $overlayer: null,
      buttonsAlign: 'right',
      disableClose: false,
      hiddenClose: false,
      animated: true,
      bodyOverflowHidden: undefined,
      _preDocumentActiveEl: null,
      disableAutoFocusWhenClose: undefined,

      allowFormControlDisable: undefined,

      needVerticalMiddleAlign: false,

      parent: document.body,

      withBodyPadding: true,
      // $el: $(this.$el)
    }
  },
  computed: {

  },
  mounted: function(){
    // this.setDefaults(DEFAULTS);
    this.$nextTick(function() {
    	this.$el = $(this.$el);
	    this.$header = this.$el.find(">header").eq(0);
	    this.$body = this.$el.find(">section").eq(0);
	    this.$footer = this.$el.find(">footer").eq(0);
	    this.$title = this.$header.find(".title");

	    this.setTitle(this.title);
	    // this.bindSelf(this._onKeyup, this.close);
	    this.setButtonAlign(this.buttonsAlign);

	    if (this.modal) {
	      this.$overlayer = $('<div class="m-window-overlayer"></div>');
	      $(this.parent).append(this.$overlayer);
	    }
	    // this.$winLayoutWrapper = $("<div class='g-window'>").appendTo(this.parent);
	    // 早于render之前，以便下级节点获得正确的参考尺寸
	    this.$el.css({ height: this.height, width: this.width });
	    // if (this.enableESC) {
	    //   $(document).on(this.$nsEventName('keyup'), this._onKeyup);
	    // }

	    if (this.enableMove) {
	      this._enableMove();
	    }
	    if (this.bodyOverflowHidden) {
	      this.$body.addClass('with-clip')
	    }

	    this._preDocumentActiveEl = document.activeElement;

	    if (this.hiddenClose) {
	      this.$el.find(".closeBtn").remove();
	    }
	    if(!this.withBodyPadding){
	      this.$el.addClass('without-padding');
	    }
	    this.show1();
    })
  },
  /**
   * @typedef {object} ButtonConfig
   * @property {string}   label - button的label
   * @property {stirng}   [type='default'] - button的类型
   * @property {function} [onClick] - button的点击回调
   * @property {boolean}  [autoClose] - 点击button是否自动关闭窗口
   * @property {string}   [className] - button classname
   * @property {string}   [id] - id属性
   * @property {boolean}  [focused=undefined] - 是否获取焦点
   * @property {booelan}  [enabled] - 是否禁用
   */

  /**
   * @param {object} [opt] - 选项参数
   * @param {boolean} [opt.animated] - 是否动画显示或关闭窗口
   * @param {string} [opt.title] - 窗口标题
   * @param {ButtonAlign}   [opt.buttonsAlign='right'] - 底部aciton按钮对齐方式
   * @param {boolean} [opt.showFooter=true] - 是否显示底部footer部分
   * @param {function} [opt.onShow] - 显示后的回调
   * @param {function} [opt.onHide] - 窗口隐藏后的回调
   * @param {number} [opt.width=600] - 窗口的宽度
   * @param {(number|string)} [opt.height='auto'] - 窗口的高度
   * @param {boolean} [opt.enableESC=true] - 是否使用esc键关闭窗口
   * @param {boolean} [opt.enableMove=true] - 是否允许窗口拖动
   * @param {Array.<ButtonConfig>} [opt.buttons] - 窗口底部按钮
   * @param {boolean} [opt.disableClose] - 是否禁用右上角close按钮
   * @param {boolean} [opt.modal] - 是否是模态窗口
   * @param {boolean} [opt.disableAutoFocusWhenClose] - 窗口关闭后是否将浏览器焦点设置到之前的对象上
   * @param {HTMLElement|Selector} [opt.parent] - Window所要挂载的层
   */
  methods: {
    setButtonAlign: function(align) {
      var preAlign = ButtonAlign[this.buttonsAlign];
      this.$footer.removeClass(preAlign);
      this.buttonsAlign = align || 'right';
      this.$footer.addClass(ButtonAlign[this.buttonsAlign]);
    },

    _mobileHook: function() {
      var self = this;
      this._disableMove();
      this._centerLayout();
      $(window).on('resize.windowView', function() {
        self._centerLayout();
      })
    },

    _centerLayout: function() {
      var width = this.$el.outerWidth(),
        height = this.$el.outerHeight();
      this.$el.css({
        'left': ($(window).width() - width) / 2,
        'top': ($(window).height() - height) / 2,
        'margin': 0
      });
    },

    _disableMove: function() {
      this.$header.off('mousedown');
    },

    _enableMove: function() {
      this._disableMove();
      // this.bindSelf(this._headerMouseDown, this._docMouseMove, this._docMouseUp);
      this.$header.mousedown(this._headerMouseDown);
    },

    _headerMouseDown: function($e) {
      this.$el.data('startX', $e.clientX);
      this.$el.data('startY', $e.clientY);
      $(document).on('mousemove', this._docMouseMove)
        .on('mouseup', this._docMouseUp);
      // for chrome ,
      // remove the text selection , or the move will be problem
      var selection = window.getSelection();
      selection.removeAllRanges();
    },

    _docMouseMove: function($e) {
      var curX = $e.clientX;
      var curY = $e.clientY;
      var deltaX = curX - this.$el.data('startX');
      var deltaY = curY - this.$el.data('startY');
      this.$el.data('startX', curX);
      this.$el.data('startY', curY);
      var curpos = this.$el.position();
      var left = curpos.left + deltaX;
      var top = curpos.top + deltaY;

      left = left < 0 ? 0 : left;
      top = top < 0 ? 0 : top;

      var winWidth = $(window).width();
      var winHeight = $(window).height();
      var width = this.$el.outerWidth();
      var height = this.$el.outerHeight();

      if (left + width > winWidth && winWidth > width) {
        left = winWidth - width;
      }
      if (top + height > winHeight && winHeight > height) {
        top = winHeight - height;
      }

      this.$el.css('left', left);
      this.$el.css('top', top);
      this.$header.css('cursor', 'move');
    },

    _docMouseUp: function($e) {
      $(document).off('mousemove', this._docMouseMove)
        .off('mouseup', this._docMouseUp);
      this.$header.css('cursor', 'default');
    },
    /**
     * 参见initialize方法中关于opt的说明
     * @param  {object} opt
     */
    show1: function(opt) {
      var self = this;
      try {
        // IE11全屏下blur()会报错
        // 获得DOM焦点，防止触发显示窗口的button再次点击后重新显示新的窗口
        document.activeElement && document.activeElement.blur();
      } catch (e) {

      }
      //
      // 解决在viewDidRendered方法中拿不到jQuery对象的问题，
      // 此处应该用PopupManager来管理，或许比较好
      //
      // this.renderTo(this.$winLayoutWrapper);

      if (!this.showFooter) {
        this.$footer.remove();
      } else {
        // if (this.buttons.length > 0) {
        //   this._renderButtons(this.buttons);
        // }
      }
      var top, winHeight = $(window).height();
      _.extend(this, opt);
      this.$el.css({
        height: this.height,
        width: this.width
      });
      this.setTitle(this.title);

      top = _.isNumber(this.top) ? this.top : 10; //(winHeight - this.$el.height())/4 ;
      top = top < 0 ? 0 : top;

      this.$el.css({
        left: ($(window).width() - this.width) / 2,
        top: top
      });

      this.enableMobileHook && this._mobileHook();
      if (this.needVerticalMiddleAlign) {
        this._centerLayout();
      }
      // if (this.animated) {
      //   this.$el.addClass('animated');
      //   this.$overlayer.addClass("animated");
      //   this.$el.animationEndOnce(function() {
      //     if (_.isFunction(self.onShow)) {
      //       self.onShow();
      //     }
      //     self.$el.removeClass('fadeInDown');
      //     self.$overlayer.removeClass("fadeIn");
      //   }).addClass("fadeInDown");
      //   this.$overlayer.addClass("fadeIn");
      // }
      // var layer = MDLayerManager.getLayer();
      // layer = this.layer || layer;
      // this.$winLayoutWrapper.css('z-index', layer);
      // this.$overlayer.css('z-index', layer);

      // MDFocusManager.setFocus(this);

    },

    _renderButtons: function(buttons) {
      var self = this;
      _.each(buttons, function(button, index) {

        var label = button.label || "",
          onClick = button.onClick,
          type = button.type || "",
          autoClose = _.isBoolean(button.autoClose) ? button.autoClose : true,
          enabled = _.isBoolean(button.enabled) ? button.enabled : true,
          $button = null;

        if (self.isEmptyString(label)) {
          return true;
        }
        var className = 'button';
        switch (type) {
          case 'danger':
            className += ' danger';
            break;
          case 'done':
            className += ' done';
            break;
          case 'pass':
            className += ' pass';
            break;
          case 'reject':
            className += ' reject';
            break;
          default:
            className += ' default';
        }
        if (button.className) {
          className += ' ' + button.className
        }

        $button = $("<button>");
        $button.text(label);
        $button.addClass(className);
        $button.prop("disabled", !enabled);

        _.isString(button.id) && $button.attr('id', button.id);
        self.$footer.append($button);
        if (button.focused && enabled) {
          $button.focus();
        }
        $button.click(function($event) {
          if (self.isFunction(onClick)) {
            onClick.call(self, $event);
          }
          if (autoClose) {
            self.close();
          }
          $event.stopPropagation();
        });
      });
    },

    close: function() {
      var self = this;
      _.isFunction(this.onClose) && this.onClose(this);
      if (this.animated) {
        this.$el.animationEndOnce(function($e) {
          self.remove();

          if (_.isFunction(self.onHide)) {
            self.onHide();
          }
        });
        this.$el.addClass("fadeOutUp");
        this.$overlayer.addClass("fadeOut");
      } else {
        self.remove();
      }
      if (!this.disableAutoFocusWhenClose) {
        this._preDocumentActiveEl && this._preDocumentActiveEl.focus();
      }

    },

    _onCloseButtonClick: function($e) {
      if (this.disableClose) {
        return;
      }
      this.close();
    },

    _onKeyup: function($e) {
      // var key = $e.keyCode;
      // // if (!MDFocusManager.isFocused(this)) {
      // //   return;
      // // }
      // if (keycode.isEscKey(key) && !this.disableClose) {
      //   this.close();
      // }
    },

    setDisableClose: function(disabled) {
      this.disableClose = disabled;
      disabled ? this.$el.addClass('disabled') : this.$el.removeClass('disabled');
      this.setActionButtonDisabled(disabled);
    },

    setFormControlDisabled: function(disabled) {
      this.$body.find('input,textarea,select,button').prop('disabled', disabled);
    },

    setActionButtonDisabled: function(disabled) {
      var $buttons = this.getActionButtons();
      $buttons.each(function() {
        $(this).prop('disabled', disabled);
        if (disabled) {
          $(this).addClass('disabled');
        } else {
          $(this).removeClass('disabled');
        }
      })
    },

    setDisabled: function(disabled) {
      this.setDisableClose(disabled);
      this.setActionButtonDisabled(disabled);
      if (this.allowFormControlDisable) {
        this.setFormControlDisabled(disabled);
      }
    },

    remove: function() {
      // MMViewBase.prototype.remove.call(this);
      this.$overlayer && this.$overlayer.remove();
      this.$winLayoutWrapper.remove();
      // $(document).off(this.$nsEventName("keyup"), this._onKeyup);
    },

    setTitle: function(title) {
      if (this.$title) {
        this.$title.text(title);
        this.title = title;
      };
    },

    getActionButtons: function() {
      var $buttons = this.$footer.find('.button');
      return $buttons;
    },

    setActionButtonDisabledByIndex: function(disabled, index) {
      var $buttons = this.getActionButtons();
      $buttons.eq(index).prop('disabled', disabled);
    },

    // default append to #bd section
    addSubView: function(subView, opt_parent, opt_slot) {
      opt_parent = opt_parent || this.$body[0];
      // MMViewBase.prototype.addSubView.call(this, subView, opt_parent, opt_slot);
    },

    addSubViewAt: function(subView, index, opt_parent, opt_slot) {
      opt_parent = opt_parent || this.$body[0];
      // MMViewBase.prototype.addSubViewAt.call(this, subView, index, opt_parent, opt_slot);
    },
    /**
     * 通过选择器获取dom
     * @param  {string} selector - button的DOM选择器
     * @return {jQuery}
     */
    getActionButton: function(selector) {
      return this.$footer.find(selector);
    },

    viewDidUnRendered: function() {
      // MMViewBase.prototype.viewDidUnRendered.call(this);
      // MDFocusManager.removeFocus(this);
      // MDLayerManager.releaseLayer();
    },

    getBody$El: function() {
      return this.$body;
    }
  }
}

</script>

<style lang="scss">
	@import '../css/helper/_mixin';
	@import '../css/component/_window'
</style>

