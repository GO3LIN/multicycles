<template>
  <b-container fluid class="token">
    <b-row align-v="center">
      <b-col>
        {{ token.name }}
        <a v-if="token.value" href="#" @click="modalShow = !modalShow">
          <edit-2-icon class="icon-right"/>
        </a>
        <div v-if="token.value" class="access-token bg-light">
          {{ token.value }}
          <a v-clipboard:copy="token.value" href="#">
            <copy-icon class="icon-right"/>
          </a>
        </div>
        <div class="sub">
          <span>Created {{ token.createdAt | ago }} ago</span>
          <a v-if="deleteToken" class="delete" href="#" @click="promptDeleteToken(token.id)">Delete</a>
        </div>
        <div class="scopes">
          <b-badge
            v-for="scope in token.scopes"
            :key="scope"
            :variant="scopeBadge(scope)"
            pill
          >{{ scope }}</b-badge>
        </div>
      </b-col>
      <b-col :md="token.value ? 5 : 12" sm="12">
        <token-stats-chart :chart-data="token.stats" class="chart"/>
      </b-col>
    </b-row>
    <b-modal
      v-model="modalShow"
      title="Update token"
      ok-title="Update"
      @ok="updateToken(token.id, updatedToken)"
    >
      <h3>Give a name to your token</h3>
      <p>Set a name to your token to help associate it with a project.</p>
      <b-form>
        <b-form-group label="Token name" label-for="tokenName" description="64 chars maximum.">
          <b-form-input
            id="tokenName"
            v-model="updatedToken.name"
            type="text"
            required
            placeholder="token name"
            maxlength="64"
          />
        </b-form-group>
      </b-form>
    </b-modal>
  </b-container>
</template>

<script>
import gql from 'graphql-tag'
import { CopyIcon, Edit2Icon } from 'vue-feather-icons'
import TokenStatsChart from './TokenStatsChart.vue'

export default {
  components: { CopyIcon, Edit2Icon, TokenStatsChart },
  props: {
    token: {
      type: Object,
      required: true
    },
    updateToken: {
      type: Function,
      required: true
    },
    deleteToken: {
      type: Function,
      required: true
    }
  },
  data() {
    return {
      modalShow: false,
      updatedToken: {
        name: this.token.name
      }
    }
  },
  methods: {
    promptDeleteToken(id) {
      if (confirm('Are-you sure to delete this token ?')) {
        this.deleteToken(id)
      }
    },
    scopeBadge(scope) {
      let variant

      switch (scope) {
        case 'vehicles:read':
        case 'providers:read':
        case 'providers:login':
          variant = 'info'
          break
        default:
          variant = 'danger'
          break
      }

      return variant
    }
  }
}
</script>

<style lang="scss">
.token {
  margin-top: 10px;
  border: 1px solid lightgray;
  border-radius: 5px;
  padding: 10px;

  .access-token {
    text-align: left;
    padding: 5px 10px;
    font-family: monospace;
  }

  .icon-right {
    height: 19px;
    position: absolute;
    right: 28px;
  }

  .sub {
    font-size: 0.8em;
    color: lightgray;

    .delete {
      position: absolute;
      right: 28px;
    }
  }

  .chart {
    height: 80px;
  }

  .scopes {
    .badge + .badge {
      margin-left: 5px;
    }
  }
}
</style>
