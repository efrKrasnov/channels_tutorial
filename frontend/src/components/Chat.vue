<template>
	<div class="content">
		<div class="contact-profile">
			<img src="http://emilcarlsson.se/assets/harveyspecter.png" alt="" />
			<p>{{ username }}</p>
			<div class="social-media">
				<i class="fa fa-facebook" aria-hidden="true"></i>
				<i class="fa fa-twitter" aria-hidden="true"></i>
				 <i class="fa fa-instagram" aria-hidden="true"></i>
			</div>
		</div>
		<div class="messages">
			<ul id="chat-log">
                <li 
                    v-for="message in messages"
                    :key="message.id"
                    :class="message.author === currentUser ? 'replies': 'sent'"
                >
                    <img src="http://emilcarlsson.se/assets/mikeross.png">
                    <p> {{ message.content }}
                        <br/>
                        <small>
                            {{Math.round((new Date().getTime() - new Date(message.timestamp).getTime()) / 60000)}} minutes ago
                        </small>
                    </p>
                </li>
			</ul>
		</div>
		<div class="message-input">
            <form @submit="sendMessageHandler">
                <div class="wrap">
                    <input 
                        @change="messageChangeHandler"
                        v-model="message"
                        required
                        id="chat-message-input" 
                        type="text" 
                        placeholder="Write your message..." 
                    />
                    <i class="fa fa-paperclip attachment" aria-hidden="true"></i>
                    <button id="chat-message-submit" class="submit">
                        <i class="fa fa-paper-plane" aria-hidden="true"></i>
                    </button>
                </div>
            </form>
		</div>
	</div>
</template>

<script>
import WebSocketInstance from '../websocket';
import Sidepanel from './sidepanel/Sidepanel.vue'
export default {
  components: { Sidepanel },
    name: 'Chat',
    props: {
        username: String
    },
    data()  {
        return {
            messages: null,
            message: ''
        }
    },
    mounted()   {
        WebSocketInstance.connect();
    },
    created()   {
        this.waitForSocketConnection(() => {
            WebSocketInstance.addCallbacks(
                this.setMessages.bind(this),
                this.addMessage.bind(this)
            );
            WebSocketInstance.fetchMessages(this.currentUser)
        })
    },
    methods: {
        waitForSocketConnection(callback)   {
            const component = this;

            setTimeout(function()  {
                if (WebSocketInstance.state() === 1)    {
                    console.log('Connection is made');
                    callback();
                    return;
                } else {
                    console.log('wait for connection...');
                    component.waitForSocketConnection(callback);
                }
            }, 100);
        },
        addMessage(message) {
            this.messages = [...this.messages, message]
        },
        setMessages(message)    {
            this.messages = message.reverse()
        },
        sendMessageHandler(e)   {
            e.preventDefault();
            const messageObject = {
                from: 'admin',
                content: this.message
            }
            WebSocketInstance.newChatMessage(messageObject);
            this.message = '';
        },
        messageChangeHandler(e) {
            this.message = e.target.value
        }
    }

}
</script>

<style>

</style>