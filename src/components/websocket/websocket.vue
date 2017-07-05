<template>
<div class="background-wrapper">
	<div class="content-wrapper">
		<div class="action-wrapper">
			发送动作<input type="text" id="action">
		</div>
		<div class="activity-id-wrapper">
			活动id<input type="text" id="activity_id">
		</div>
		<div class="activity-title-wrapper">
			活动标题<input type="text" id="activity_title">
		</div>
		<div class="verify-wrapper">
			审核<input type="text" id="verify">
		</div>
		<div class="send-content">
			<div>发送内容</div><textarea id="send-content-id"></textarea>
		</div>
		<div class="receive-content">
			收到的内容
			<ul>
				<li v-for="content in contentList">
					<div>
						{{content.msg}}
					</div>				
				</li>
			</ul>
		</div>
		<button @click="connect">连接</button>
		<button @click="send">发送</button>
	</div>
</div>
</template>

<script type="text/esmascript-6">
export default {
	data() {
		return {
			ws_server: '',
			ws_cube: '',
			contentList: [{
  				msg: "1"
  			},{
  				msg:"2"
  			}],
			action: "",
			activity_title: "",
			verify: "",
			content: "",
			send_content: ""
		}
	},
	methods: {
		connect() {
			this.ws_server = new WebSocket('ws://127.0.0.1:8081');
			let self = this;
			self.ws_server.onopen = function(e) {
				alert("connect");
			}
			self.ws_server.onmessage = function(e) {
				let msg;
				msg = e.data;
				//console.log(msg);
				if(msg.replace(/(^s*)|(s*$)/g, "").length != 0){
					msg = JSON.parse(msg);
					self.contentList.push({
						msg:msg
					})
					/*if(msg.HEAD.record_type=="SYNI") {
						;
					}*/
				}
			}
		},
		send() {
			let action_content = document.getElementById("action");
			this.action = action_content.value;

			let verify_result = document.getElementById("verify");
			this.verify = verify_result.value;

			let activity_id_content = document.getElementById("activity_id");
			this.activity_id = activity_id_content.value;

			let activity_title_content = document.getElementById("activity_title");
			this.activity_title = activity_title_content.value;

			this.send_content = {action:this.action,data:{verify:this.verify,activityID:this.activity_id,activityTitle:this.activity_title}};
			this.send_content = JSON.stringify(this.send_content);

			console.log(this.send_content);

			this.ws_server.send(this.send_content);
		}
	}
};
</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>
