<template>

    <div class="chat">
        <h3>MENSAJES ENVIADOS por hacer</h3>
        <textarea name="" id="" cols="30" rows="10"></textarea>
    </div>
	<form :action="sendMessage" @click.prevent="onSubmit" >
		<input v-model="message" type="text" />
		<input type="submit" value="Send" @click="sendMessage" />
	</form>

    <div class="container">
        <label>File
            <input type="file" name="file" id="inputFile">

        </label>

        <button @click="onUpload()">Enviar</button>
    </div>
        
  


	<div v-if="showMsg">
		<h3>Message in the WebSocket</h3>
		<p>
			{{ rcvMessage }}
		</p>
		<button @click="showMsg = !showMsg">Dismiss</button>
	</div>
</template>

<script>
export default {
	name: "App",

	data() {
		return {
			message: "",
            File: null,
			socket: null,
			rcvMessage: "",
			showMsg: false,
            upoadValue: 0,
		};
	},


	mounted() {
		this.socket = new WebSocket("ws://localhost:9100/socket");
		this.socket.onmessage = (msg) => {
			this.acceptMsg(msg);
		};
	},
	methods: {
		sendMessage() {
			let msg = {
				message: this.message,
			};
			this.socket.send(JSON.stringify(msg));
		},

		acceptMsg(msg) {
			this.rcvMessage = msg.data;
			this.showMsg = true;
		},

        onUpload(){
            let inputFile = document.getElementById("inputFile");
            let file = inputFile.files[0];

            let formData = new FormData();
            formData.append("file", file);
            // formData.append("name", file.name);

            // let headers = {
            //     "Content-Type": "multipart/form-data"
            // }
            
            fetch("http://localhost:9100/upload", {
                method: "POST",
                body: formData,
                mode:"no-cors"
            }).then(res => {
                console.log(res.body);
            })
        }

	},
};
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
