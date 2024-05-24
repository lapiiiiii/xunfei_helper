<style lang="scss" scoped="scoped">
.vvoice-play{ width:150rpx;	
	&.flex{ justify-content: flex-end;}
	.vp-bg {
		background:url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGAAAAAYCAYAAAAF6fiUAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMDY3IDc5LjE1Nzc0NywgMjAxNS8wMy8zMC0yMzo0MDo0MiAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6NzlFRDZDRDNENzlFMTFFNkJDN0NFMjA2QTFFRTRDQkIiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6NzlFRDZDRDJENzlFMTFFNkJDN0NFMjA2QTFFRTRDQkIiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6MTAxQkEzQ0RENzM2MTFFNjgyMEI5MTNDRkQ0OTM5QUEiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6MTAxQkEzQ0VENzM2MTFFNjgyMEI5MTNDRkQ0OTM5QUEiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4K4iKVAAACUUlEQVR42uSazytEURTHvTHjR4kaU8xsSDZSdmbjx4oSK8XGQrJlpSwYTSmxEWWhUIpsZK3kD7DRNBuSBZFCNjZ+JPKcV6ecXu/d3sy7595bc+vbfXPue5/749z77o83lm3bZYYFC8RZqAbQAigP2tXNj5aZF7gdkAZNk9+7WvnOCCgxRUCb9n/o1sk3pUH6QDHF/GNsoM+QeYfiy6qkFeLZDBb0GlTB4AAR/xXT9nXxZVa0WCekQd9Y0HOJjg3CHySviiZmfjO3AyIhnu0gBc0wjAIR/wLtW8z87aAOWAI9gqaYRoAff4ZUoi7EKCiUP462j4CdSCrfK4N1Ahpi6I0i/hPa50M4oFB+Dbm/SzXfL5MD4rUogxP8+Itozynm59E+q5ovyuQdHxphWh568XvR5kxq1SEn40L4e0XMA1L4EcEe7RTjLqYdqRf/gezQUwr5LxjXq+aLHPCFcTmTA7z4tutIQhXfLiJPKXyRA/oxzgW8v9DgxU+S62eF/ATGr6r5fg26Corj9RHD4Z0fvwfjS9CbQn4bxrfK+R6TyzxZNk260solTL4i/g3al10TsMXIryA72T7VfK8MnJO8X9CKy14lafXjxx8jFUsSeyUzfxhtPwHPoqTy/TJJMJzJiPgNpJdsuNJizPwztB/q4JtwHN2KW3sn3HuMOouR30l6bbsOvgkOyGIBnaPbRldalJl/h2knuvgmOKAWNAFKMUz4Iv4O6Z1xXXxTPxtazHy6khnVyS/Fb8IDpHGyuvmWgX9L4Q4toDnQFWhNN/9PgAEAR4w1ULjdCbEAAAAASUVORK5CYII=) right 0 no-repeat;
		width: 40rpx;
		height: 40rpx;
		background-size: 400%;
		transform: rotate(-180deg);
	}	 
	.voicePlay {
		animation-name: voicePlay;
		animation-duration: 1s;
		animation-direction: normal;
		animation-iteration-count: infinite;
		animation-timing-function: steps(3);
	}
	.miao{ color:#fff; font-size:32rpx; margin-right:20rpx;}
	&.l{
		.vp-bg{ background:url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGAAAAAYCAYAAAAF6fiUAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDYuMC1jMDAyIDc5LjE2NDQ2MCwgMjAyMC8wNS8xMi0xNjowNDoxNyAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6MkFEMEMwNjBCODJDMTFFQ0IxQTM4OUM3QzVCRERERDgiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6MkFEMEMwNUZCODJDMTFFQ0IxQTM4OUM3QzVCRERERDgiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6NzlFRDZDRDJENzlFMTFFNkJDN0NFMjA2QTFFRTRDQkIiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6NzlFRDZDRDNENzlFMTFFNkJDN0NFMjA2QTFFRTRDQkIiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4B4LG4AAADdElEQVR42syZS2jUUBSGk1SHEUFBC6KIlVIERdz4wtdGEMRuBMHCWFcuXFQK4spW6KaCiIigIPjozo2CC0HBigsFXQiiFEfBRRdVyiAFXUhRWo3/xZNwcifJ5L5CDvycZG7nm/Sc+zzxwzD0TGxD7xbPsvlQ6LmzNdAF6B00YQqbmf4oxyPmo60jf4lXPXOdgF3QGXY/4YovOmenJAQVTMBfx/zH0E26vgvtd8lHEnL5fgWmINEJDkIvoAUHAc/ii0AdgX5BKwx+O+ajty+wuCT4vM3FCPANvnseegZ9gGoOEpDF74d+QHUaCcZ8BL3G1oZCfFsJCA2SIB5+EdoEvbKY2CL8E+RPQj0u+UjORtdrgG6w3kDb6XoHNCol1tTy+E+gJl2PaO6CEnwEepS1deQHKXP6ODQLDWmMgiI2Ds1CnD8FnWPtKw0Sq8q/zEZBoMNHoBN8xC6NP4jPgyIjYDm0FrqBL/Q7SEDMp3k4sqtQi66HDHq8Kv8h+WXQHl0+kqDFD1KG1Fm4e3T7AEmwfVZI8KWzyC3yxwymIVX+T+g9Xe9V5UvxaeMjnrn8IGNeG4Sbp6wddbAzyeI/J7+N2nSnIVX+FPk+W3wkphA/b86bJL/P0YEojf+FndDXlcj/Rn512fy8BPwm3+UoAWn80OIWVIUfavymFX5eAg6Rf8t2SDa3rW18Wtwia5XI7yb/vWx+kFFeuAK3ii1ktot3WfwD5Ju0OJbF30x+2hafFt+O/LRzwAjb054CaJ41/7EQ/ASfFrPITpN/ZDAVqfJrdECLDm1KfCk+bXwqT2Ty00bAepqzLvFSKkB13NtIQMz3kqXgBtRL19elAIUO+f00ssX/9lKXj/ho8VOroYB1I9hz7N7HfZgxXekkQcyJc+xevMT4Sg8qHn6YtS3VqFSq8F/TAek+NKDKFy9k6CVMzMdnwyw+MR+fDxRKgIpZKEf3Uc8QC9iMVBSr024jdMTfyaaFraxuo81HkHtYbBJ8tDVdFuN0rUEPL0q3u6W2RQsFuTx+tIA+1Qx+YX5a8G3uakzsGvXMSWnr1kUJcMW/zUZDwwYfQW6x3l+I71fwpXy083H5XljsZC7S9XFpK6lk8kt5tpOM+fibTH4V3wl7joN/mAVnzCT4GR0ywc8LflWmoLLtM3QH+uT9L1E741OJOtf+CTAA7S5qFA1h3jwAAAAASUVORK5CYII=)  right 0 no-repeat;
			transform: rotate(0deg);
		}
		.miao{ color:#1D2129; margin-right:0; margin-left:20rpx;}
	}
}
@keyframes voicePlay {
	0% {
		background-position: 0;
	}
	100% {
		background-position: 100%;
	}
}
</style>
<template>
	<view class="vvoice-play flex" :class="{'l':wz=='l'}">
		<view class="miao" v-if="wz=='r'">{{duration}}''</view>
	  <view class="vp-bg" :class="{voicePlay:play}"></view>
		<view class="miao" v-if="wz=='l'">{{duration}}''</view>
	</view>
</template>
 
<script>
 
export default{
	props:{
		duration:{
			type:Number,
			default:0
		},		
		wz:{
			type:String,
			default:'l'
		},
		play:{
			type:Boolean,
			default:false,
		}
	},	
	
	data(){
		return{			
		}
	},
	
	methods:{
	
		
		
		
	}
	
}
</script>
 
<style>
</style>