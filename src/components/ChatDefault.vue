<template>
    <div class="chat">
        <div class="container">
            <div class="switcher">
                <div class="switcher-group">
                    <span
                        class="switcher__name switcher__name-current"
                        :class="{changed: !checked}"

                    >
                        {{ getUsers[1].name }}
                    </span>
                    <input
                        type="checkbox"
                        id="cbx"
                        style="display:none"
                        v-model="checked"
                        @change="userChange"
                    />
                    <label for="cbx" class="toggle"><span></span></label>
                    <span
                        class="switcher__name switcher__name-other"
                        :class="{changed: checked}"
                    >
                        {{ getUsers[0].name }}
                    </span>
                </div>
                <button @click="logout">Выход</button>
            </div>
        </div>
        <div class="container">
            <div class="row">
                <div class="col friends">
                    <h2 class="friends__header">Список друзей:</h2>
                    <ul class="friends__list">
                        <li v-for="user in getUsers"
                            :key="user.id"
                            :data-id="user.id"
                            @click="chooseChat"
                            :class="{saved: user.current}"
                            >
                            <img :src='user.avatar' alt="Сергей Петров">
                            <p class="name">{{ user.name }} <span v-if="user.current">(Saved Messages)</span>
                                <span class="text">
                                        <!--ge.messages[user.messages.length - 1]-->
                                </span>
                            </p>
                        </li>
                    </ul>
                </div>
                <div class="col messages">
                    <div class="messages__bubbles">
                        <div
                            class="messages__group"
                            v-for="message in getMessages"
                            :key="message.id"
                            :class="currentUser.id === message.userId ? '' : 'messages__group-from'"
                        >
                                <img src="@/assets/images/empty-profile.jpg" alt="Иван Иванов">
                                <p>{{ message.text }}</p>
                        </div>

                    </div>
                    <div class="messages__input">
                        <textarea
                            placeholder="Введите ваше сообщение"
                            name="message"
                            v-model="message"
                        ></textarea>
                        <button
                            class="submit"
                            @click="sendMessage"
                        >
                            <svg id="Layer_1" style="enable-background:new 0 0 30.2 30.1;" version="1.1" viewBox="0 0 30.2 30.1" xml:space="preserve" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
                            <path class="st0" d="M2.1,14.6C8.9,12,28.5,4,28.5,4l-3.9,22.6c-0.2,1.1-1.5,1.5-2.3,0.8l-6.1-5.1l-4.3,4l0.7-6.7l13-12.3l-16,10  l1,5.7l-3.3-5.3l-5-1.6C1.5,15.8,1.4,14.8,2.1,14.6z"/></svg>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss">
    .row {
        border: 2px solid #e3e4e8;
        -webkit-border-radius: 5px;
        -moz-border-radius: 5px;
        border-radius: 5px;
    }
    @media (max-width: 760px) {
        .row {
            flex-direction: column;
        }
    }
</style>

<script>
    export default {
        name: 'ChatDefault',
        data () {
            return {
                message: '',
                messageTo: '',
                arrayMessages: [],
                saved: false,
                checked: false
            }
        },
        methods: {
            sendMessage() {
                const id = Number(this.messageTo)
                const message = {
                    userId: this.currentUser.id,
                    to: id,
                    text: this.message
                }
                this.$store.dispatch('newMessage', message)
                if (this.saved) {
                    this.arrayMessages = this.getAllMessages.filter(function(message) {
                        return message.userId === id && message.to === id
                    })
                } else {
                    this.arrayMessages = this.getAllMessages.filter(function(message) {
                        return (message.userId === id || message.to === id)
                            && (message.userId !== message.to)
                    })
                }
                this.$store.dispatch('filterMessages', this.arrayMessages)

                this.message = ''
            },
            chooseChat(event) {
                const chats = document.querySelectorAll('.friends__list li')
                const id = event.currentTarget.getAttribute('data-id')
                this.messageTo = id

                if (event.currentTarget.classList.contains('saved')) {
                    this.saved = true
                    this.arrayMessages = this.getAllMessages.filter(function(message) {
                        return message.userId === Number(id) && message.to === Number(id)
                    })
                } else {
                    this.saved = false
                    this.arrayMessages = this.getAllMessages.filter(function(message) {
                        return (message.userId === Number(id) || message.to === Number(id))
                            && (message.userId !== message.to)
                    })
                }
                this.$store.dispatch('filterMessages', this.arrayMessages)

                chats.forEach(chat => {
                    chat.classList.remove('active')
                })
                event.currentTarget.classList.add('active')
            },
            userChange() {
                this.$store.dispatch('userChange')
            },
            logout() {
                this.$store.dispatch('logoutUser')
                this.$router.push('/')
            }
        },
        computed: {
            getUsers() {
                return this.$store.getters.getUsers
            },
            getMessages() {
                return this.$store.getters.getMessages
            },
            getAllMessages() {
                return this.$store.getters.getAllMessages
            },
            currentUser() {
                return this.$store.getters.getCurrentUser
            }
        }
    }
</script>
