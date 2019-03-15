<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      xs12
      sm8
      md6
    >
      <v-card>
        <v-card-title class="headline">Liste des utilisateurs</v-card-title>
        <v-card-text>
          <v-data-table
            :items="users"
          >
            <template v-slot:items="props">
              <td>{{ props.item.id }}</td>
              <td>{{ props.item.username }}</td>
              <td>{{ props.item.email }}</td>
              <td>{{ props.item.company.name }}</td>
              <td class="text-xs-right"><v-btn
            color="primary"
            flat
            nuxt
            :to="'/user/'+props.item.id"
          >Détails</v-btn></td>
            </template>
          </v-data-table>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import axios from 'axios'

export default {
  components: {
    Logo,
    VuetifyLogo
  },
  data() {
    return {
      users: []
    }
  },
  mounted () {
    axios
      .get('https://jsonplaceholder.typicode.com/users')
      .then(response => {console.log(response);
        this.users = response.data
      })
  }
}
</script>

<style>
body, p {
  font-family: 'Nunito', sans-serif;
}

</style>
