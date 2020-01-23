<template>
  <app-page>
    <template v-slot:main>

      <full-centered v-slot:main v-if="loading || oops">
        <loading-checkmark visible="false" :loading="loading" v-if="!oops"></loading-checkmark>
        <oops v-show="oops"></oops>
      </full-centered>

      <simple-box :title="$t('app.users')" v-show="!loading && !oops">
        <p><strong>{{ rows.length }}</strong> users</p>


          <table class="table table-sm">
            <thead>
              <tr>
                <th scope="col">Username</th>
                <th scope="col">Firstname</th>
                <th scope="col">Lastname</th>
                <th scope="col">E-mail</th>
                <th scope="col">Referral Code</th>
                <th scope="col">Created at</th>
                <th scope="col">Last Login</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(user, index) in rows" :key="index">
                <td>{{ user.username }}</td>
                <td>{{ user.firstname }}</td>
                <td>{{ user.lastname }}</td>
                <td>{{ user.email }}</td>
                <td>{{ user.referralCode }}</td>
                <td>{{ $d(Date.parse(user.createdAt), 'long') }}</td>
                <td>{{ $d(Date.parse(user.lastLogin), 'long') }}</td>
              </tr>
            </tbody>
          </table>

      </simple-box>

    </template>
    <router-view/>
  </app-page>
</template>

<script>
import AppPage from 'seed-theme/src/layouts/AppPage.vue';
import { reactive, toRefs } from '@vue/composition-api';

export default {
  name: 'Users',
  components: {
    AppPage,
  },
  setup(props, context) {
    const data = reactive({
      loading: false,
      oops: false,
      rows: [],
    });

    async function getRows() {
      try {
        data.loading = true;
        data.oops = false;
        const response = await context.root.axios.get('/stats/users');
        data.rows = response.data;
        data.loading = false;
      } catch (error) {
        data.loading = false;
        data.oops = true;
      }
    }

    getRows();

    return {
      ...toRefs(data),
    };
  },
};

</script>
