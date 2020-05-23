<template>
  <div class="home">
    <div class="container">
      <div class="form">
        <h1>Войти в чат</h1>
        <div class="form__group">
          <label for="name">Ваше имя</label>
          <input
              type="text"
              id="name"
              v-model="name"
              placeholder="Введите имя"
          >
        </div>
        <div class="form__group">
          <label for="file">Загрузите изображение</label>
          <input id="file" @change="previewThumbnail" name="thumbnail" type="file">
          <img :src="avatar" alt="">
        </div>
        <div class="form__group form__group-button">
          <button
                  @click="createUser"
          >Войти в чат</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
  export default {
    name: 'Home',
    data() {
      return {
          name: '',
          avatar: ''
      }
    },
    methods: {
      previewThumbnail: function(event) {
        let input = event.target

        if (input.files && input.files[0]) {
          let reader = new FileReader()

          reader.onload = e => {
            this.avatar = String(e.target.result)
          }
          reader.readAsDataURL(input.files[0])
        }
      },
      createUser() {
        if (this.name !== '' && this.avatar !== '') {
          const user = {
            id: 2,
            name: this.name,
            avatar: this.avatar,
            current: true
          }
          this.$store.dispatch('createUser', user)
          this.$router.push('/chat')
        } else {
          alert('Введите имя или загрузите изображение')
        }
      }
    }
  }
</script>
