<template>
	<transition  name="fade">
		<div >
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
				closed: false
			}
		},
		computed: {

		},
		methods: {
			_mousedown: function($e) {
				this.down = true;
				console.log($e)
			    $(this.$el).data('startX', $e.clientX);
			    this.$el.data('startY', $e.clientY);
			    $(document).on('mousemove.moveBox', this._docMouseMove)
			    		   .on('mouseup.moveBox', this._docMouseUp);
			},
			_docMouseMove: function($e) {
			    var curX = $e.clientX;
			    var curY = $e.clientY;
			    var deltaX = curX - this.$el.data('startX');
			    var deltaY = curY - this.$el.data('startY');
			    this.$el.dataset.startX = curX;
			    this.$el.dataset.startY = curY;
			    var curpos = this.$el.position();
			    var left = curpos.left + deltaX;
			    var top = curpos.top + deltaY;
			    left = left < 0 ? 0 : left;
			    top = top < 0 ? 0 : top;
			    var winWidth = $(window).width();
			    var winHeight = $(window).height();
			    var width = this.$el.outerWidth();
			    var height = this.$el.outerHeight();
			    if (left + width > winWidth) {
			      left = winWidth - width;
			    }
			    if (top + height > winHeight) {
			      top = winHeight - height;
			    }
			    this.$el.css('left', left);
			    this.$el.css('top', top);
			    this.$('>header').css('cursor', 'move');
			}
		}
	}
</script>