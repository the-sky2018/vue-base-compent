<template>
  <transition>
  	<div>
	    <div class="m-window" tabindex="1"
	    :style="position">
	      <header  
	      	@mousedown="_headerMouseDown"
			@mousemove="_mouseMove"
			@mouseup="_mouseUp"
			@mouseleave="_mouseUp"
			:class="[{dragging: dragging}, 'window-header']"
	      	>
	        <span class="title">{{title}}</span>
	        <button class="icon closeBtn"></button>
	      </header>
	      <section class="window-body">
	      	<slot name="content"></slot>
	      </section>
	      <footer class="window-footer">
	      	<slot name="footer"></slot>
	      </footer>    
	    </div>
		<div v-if="modal" class="m-window-overlayer"></div>
	</div>
  </transition>
</template>
<script>

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
	modal: {
		default: false
	}
  },

  data: function() {
    return {
      start: {x:0, y:0},

      position: {left:'200px', top:'40px', width: "400px", height: "200px"},

      dragging: false
    }
  },

  mounted: function(){
    this.$nextTick(function() {
		var winWidth = window.innerWidth;
		var curwidth = this.$el.children[0].offsetWidth;
		var left = Math.ceil((winWidth-curwidth)/2);
		this.position.left = left + "px";
    })
  },
 
  methods: {
    _headerMouseDown: function(e) {
		this.dragging = true
		this.start.x = e.pageX
		this.start.y = e.pageY
    },

    _mouseMove: function(e) {
		if(!this.dragging) {
			return;
		}
		var curX = e.pageX;
		var curY = e.pageY;
		var deltaX = curX - this.start.x; 
		var deltaY = curY - this.start.y;
		var left = parseInt(this.$el.children[0].style.left) + deltaX;
		var top = parseInt(this.$el.children[0].style.top) + deltaY;
		left = left < 0 ? 0 : left;
		top = top < 0 ? 0 : top;
		var winWidth = window.innerWidth;
		var winHeight = window.innerHeight;
		var width = this.$el.children[0].offsetWidth;
		var height = this.$el.children[0].offsetHeigh;
		if (left + width > winWidth && winWidth > width) {
			left = winWidth - width;
		}
		if (top + height > winHeight && winHeight > height) {
			top = winHeight - height;
		}
		this.position.left = left +"px";
		this.position.top = top +"px";
		this.start.x = curX;
		this.start.y = curY;
    },

    _mouseUp: function($e) {
		if(this.dragging) {
			this.dragging = false
		}
    },

  }
}

</script>

<style lang="scss">
	@import '../css/helper/_mixin';
	@import '../css/component/_window';
	.dragging{
		cursor: move
	}
</style>

