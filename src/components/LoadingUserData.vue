<template>
  <div class="terminal">
    <div v-if="loading" class="data-loading">
      <h1>TICKETER</h1>
      <p>Please wait a few moments...</p>
    </div>
  </div>
</template>

<style>
.hidden {
  display: none;
}

.terminal {
  margin-top: 10vh;
  text-align: center;
  padding: 10px;
  border-radius: 5px;
  width: 80%;
  max-width: 600px;
  margin: auto;
}

.terminal h1 {
  margin-bottom: 20px;
}

.loading-text {
  margin-top: 10px;
  font-weight: bold;
}
</style>

<script>
import axios from 'axios';

export default {
  name: 'LoadingUserData',
  data() {
    return {
      loading: true,
      companies: [],
      allRoutes: [],
      allFleets: [],
    };
  },
  async mounted() {
    try {
      const username = localStorage.getItem('username');
      const userId = localStorage.getItem('user_id');
      if (!username) throw new Error('No username found.');

      // Fetch conversations
      await this.fetchConversations(userId);

      // Fetch companies
      await this.fetchCompanies(userId);

      // Fetch routes and fleets for each company
      for (const company of this.companies) {
        await this.fetchRoutesForCompany(company);
        await this.fetchFleetForCompany(company);
      }

      // Save data to localStorage
      this.saveToLocalStorage();
    } catch (error) {
      console.error('Failed to fetch data:', error);
      alert('Failed to load data. Please try again later.');
    } finally {
      this.loading = false;
      setTimeout(() => {
        this.redirect();
      }, 5000);
    }
  },
  methods: {
    redirect() {
      this.$router.push({ path: `/companySelect` }); // Assuming target contains a valid route name
    },
    async fetchConversations() {
      //const responses = await Promise.all([
      //  axios.get('https://https://www.mybustimes.cc/api/conversation/', { params: { format: 'json', to: userId } }),
      //  axios.get('https://https://www.mybustimes.cc/api/conversation/', { params: { format: 'json', From: userId } }),
      //]);

      //localStorage.setItem('UserToData', JSON.stringify(responses[0].data.results || []));
      //localStorage.setItem('UserFromData', JSON.stringify(responses[1].data.results || []));
    },
    async fetchCompanies(userId) {
      const companyResponse = await axios.get('https://www.mybustimes.cc/api/operator/', { params: { format: 'json', owner: userId } });
      this.companies = companyResponse.data.results || [];
      localStorage.setItem('operators', JSON.stringify(this.companies));

    },
    async fetchRoutesForCompany(company) {
      try {
        const response = await axios.get('https://www.mybustimes.cc/api/operator/route/?limit=99999', { params: { format: 'json', route_operators: company.id } });
        company.routes = response.data.results || [];
      } catch (error) {
        console.error(`Failed to fetch routes for ${company.operator_name}:`, error);
      }
    },
    async fetchFleetForCompany(company) {
      try {
        const response = await axios.get('https://www.mybustimes.cc/api/operator/fleet/?limit=99999', { params: { format: 'json', operator: company.id } });
        company.fleet = response.data.results || [];
      } catch (error) {
        console.error(`Failed to fetch fleet for operator ${company.id}:`, error);
      }
    },
    saveToLocalStorage() {
      this.allRoutes = this.companies.flatMap(company => company.routes || []);
      this.allFleets = this.companies.flatMap(company => company.fleet || []);
      localStorage.setItem('routes', JSON.stringify(this.allRoutes));
      localStorage.setItem('fleets', JSON.stringify(this.allFleets));
    }
  },
};
</script>