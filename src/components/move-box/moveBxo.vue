<template>
	<transition  name="fade">
		<div class="move-box">
			<div class="title" @mousedown="_mousedown">124466</div>
		</div>
	</transition>
</template>

<script>
	export default { 
		name: 'moveBox', 
		props: {
			// contentTemp: '',
		},
		data () {
			return {
				closed: false,
				down: false,
				$root : $(this.$el)
			}
		},
		computed: {

		},
		methods: {
			_mousedown: function($e) {
				this.down = true;
				console.log($e)
			    this.$root.data('startX', $e.clientX);
			    this.$root.data('startY', $e.clientY);
			    $(document).on('mousemove.moveBox', this._docMouseMove)
			    	.on('mouseup.moveBox', this._docMouseUp);
			},
			_docMouseMove: function($e) {
			    var curX = $e.clientX;
			    var curY = $e.clientY;
			    var deltaX = curX - this.$root.data('startX');
			    var deltaY = curY - this.$root.data('startY');
			    this.$root.data("startX", curX);
			    this.$root.data("startY", curY);
			    var curpos = this.$root.position();
			    var left = curpos.left + deltaX;
			    var top = curpos.top + deltaY;
			    left = left < 0 ? 0 : left;
			    top = top < 0 ? 0 : top;
			    var winWidth = $(window).width();
			    var winHeight = $(window).height();
			    var width = this.$root.outerWidth();
			    var height = this.$root.outerHeight();
			    if (left + width > winWidth) {
			      left = winWidth - width;
			    }
			    if (top + height > winHeight) {
			      top = winHeight - height;
			    }
			    this.$root.css('left', left);
			    this.$root.css('top', top);
			    this.root.find('.title').css('cursor', 'move');
			}
		},
		_docMouseUp: function($e) {
		    $(document).off('mousemove.moveBox', this._docMouseMove)
		    	.off('mousemove.moveBox', this._docMouseUp)
		    this.root.find('.title').css('cursor', 'default');
		  },
	}
</script>
<style lang="scss">
	.move-box{
		position: fixed;
		overflow-y: auto;
		z-index: 10001;
		left:0;
		top: 0;
		width: 200px;
		height: 200px;
		border: solid 1px;
	}
</style>