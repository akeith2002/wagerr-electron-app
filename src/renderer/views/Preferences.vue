<template>
  <div id="preferences" class="content row">
    <div class="col s12">
      <h4>Preferences</h4>

      <div class="row text-center">
        <div class="col s6">
          <div class="preferences-block table-container">
            <table class="main-table card z-depth-2">
              <tbody>
                <tr class="info-row">
                  <td>Odds Display</td>
                  <td>
                    <div id="oddsChoice">
                      <form action="#" @submit.prevent>
                        <p>
                          <label>
                            <input
                              name="oddsFormat"
                              type="radio"
                              id="odds_choice_decimal"
                              :value="getOddsFormats.decimal"
                              @change="changeOddsFormat"
                              :checked="
                                oddsFormatChecked(getOddsFormats.decimal)
                              "
                            />
                            <span>Decimal</span>
                          </label>
                        </p>
                        <p>
                          <label>
                            <input
                              name="oddsFormat"
                              type="radio"
                              id="odds_choice_fraction"
                              :value="getOddsFormats.fraction"
                              @change="changeOddsFormat"
                              :checked="
                                oddsFormatChecked(getOddsFormats.fraction)
                              "
                            />
                            <span>Fraction</span>
                          </label>
                        </p>
                        <p>
                          <label>
                            <input
                              name="oddsFormat"
                              type="radio"
                              id="odds_choice_american"
                              :value="getOddsFormats.american"
                              @change="changeOddsFormat"
                              :checked="
                                oddsFormatChecked(getOddsFormats.american)
                              "
                            />
                            <span>American</span>
                          </label>
                        </p>
                      </form>
                      <br />
                    </div>
                  </td>
                </tr>
                <tr class="info-row">
                  <td>Include Wagerr network share in betting odds?</td>
                  <td>
                    <div id="show-network-share-choice">
                      <p>
                        <label>
                          <input
                            name="showNetworkShare"
                            type="checkbox"
                            id="showNetworkShare"
                            :value="getShowNetworkShare"
                            @change="toggleShowNetworkShare"
                            :checked="getShowNetworkShare"
                          />
                          <span style="padding-left: 0px;"></span>
                        </label>
                      </p>
                      <br />
                    </div>
                  </td>
                </tr>
                <tr class="info-row">
                  <td>Open wagerr.conf file</td>

                  <button class="btn" @click="onOpenConf">Open</button>
                </tr>
                <tr class="info-row">
                  <td>Backup wallet</td>

                  <button class="btn" @click="backupWallet">Export</button>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { remote, shell } from 'electron';
import Store from 'electron-store';
import { mapGetters, mapActions } from 'vuex';
import wagerrRPC from '@/services/api/wagerrRPC';
import { getWagerrConfPath } from '../../main/blockchain/blockchain';

export default {
  name: 'Preferences',

  methods: {
    ...mapActions(['updateOddsFormat', 'toggleShowNetworkShare']),
    changeOddsFormat: function(event) {
      this.$store.dispatch('updateOddsFormat', Number(event.target.value));
    },

    oddsFormatChecked: function(format) {
      return format == this.getOddsFormat;
    },

    onOpenConf: function() {
      shell.openItem(this.confPath);
    },

    backupWallet: function() {
      let folderPath = remote.dialog.showOpenDialog({
        title: 'Backup Wallet.dat file.',
        buttonLabel: 'Select Folder',
        properties: ['openDirectory'],
        buttons: ['Confirm', 'Cancel'],
        cancelId: 1,
        defaultId: 0
      });

      if (folderPath) {
        wagerrRPC.client
          .backupWallet(folderPath)
          .then(function(resp) {
            console.log(resp);
            M.toast({
              html:
                '<span class="toast__bold-font">Success &nbsp;</span> Wallet backup up located here: ' +
                folderPath,
              classes: 'green'
            });
          })
          .catch(function(err) {
            M.toast({ html: err, classes: 'wagerr-red-bg' });
            console.log(err);
          });
      }
    }
  },

  computed: {
    ...mapGetters(['getOddsFormats', 'getOddsFormat', 'getShowNetworkShare'])
  },

  data: function() {
    return {
      confPath: getWagerrConfPath()
    };
  }
};
</script>

<style lang="scss" scoped>
@import '../assets/scss/_variables.scss';

.preferences-block {
  table {
    thead {
      th {
        text-align: center;
        font-size: 18px;
      }
    }

    tbody {
      th {
        padding: 10px 15px;
        font-size: 15px;
        line-height: 20px;
        vertical-align: top;
        white-space: nowrap;
      }

      td {
        padding: 10px 15px !important;
        font-size: 14px;
        line-height: 20px;
        vertical-align: top;
        #show-network-share-choice span.isActive {
          text-decoration: none;
        }
        #show-network-share-choice span {
          text-decoration: line-through;
        }
      }
    }
  }

  button {
    margin: 5px;
  }

  [type='radio']:checked + span:after {
    background-color: $wagerr_red;
    border: 2px solid $wagerr_red;
  }

  [type='checkbox']:checked + span:not(.lever):before {
    border-right: 2px solid $wagerr_red;
    border-bottom: 2px solid $wagerr_red;
  }
}
</style>
