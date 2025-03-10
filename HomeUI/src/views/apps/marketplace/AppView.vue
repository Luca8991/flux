<template>
  <div class="app-details">
    <!-- App Header -->
    <div class="app-detail-header">
      <!-- Header: Left -->
      <div class="app-header-left d-flex align-items-center">
        <span class="go-back mr-1">
          <feather-icon
            :icon="$store.state.appConfig.isRTL ? 'ChevronRightIcon' : 'ChevronLeftIcon'"
            size="20"
            class="align-bottom"
            @click="$emit('close-app-view')"
          />
        </span>
        <h4 class="app-name mb-0">
          {{ appData.name }}
        </h4>
      </div>
    </div>

    <!-- App Details -->
    <vue-perfect-scrollbar
      :settings="perfectScrollbarSettings"
      class="app-scroll-area scroll-area"
    >
      <b-row class="match-height">
        <b-col
          xxl="9"
          xl="8"
          lg="8"
          md="12"
        >
          <b-card title="Details">
            <b-form-textarea
              id="textarea-rows"
              rows="2"
              readonly
              :value="appData.description"
              class="description-text"
            />
            <b-card
              class="mt-1"
              no-body
            >
              <b-tabs @activate-tab="componentSelected">
                <b-tab
                  v-for="(component, index) in appData.compose"
                  :key="index"
                  :title="component.name"
                >
                  <list-entry
                    title="Description"
                    :data="component.description"
                  />
                  <list-entry
                    title="Repository"
                    :data="component.repotag"
                  />
                  <b-card
                    v-if="component.userEnvironmentParameters"
                    title="Parameters"
                    border-variant="primary"
                  >
                    <b-tabs v-if="component.userEnvironmentParameters">
                      <b-tab
                        v-for="(parameter, paramIndex) in component.userEnvironmentParameters"
                        :key="paramIndex"
                        :title="parameter.name"
                      >
                        <div class="form-row form-group">
                          <label class="col-2 col-form-label">
                            Value
                            <v-icon
                              v-b-tooltip.hover.top="parameter.description"
                              name="info-circle"
                              class="mr-1"
                            />
                          </label>
                          <div class="col">
                            <b-form-input
                              id="enviromentParameters"
                              v-model="parameter.value"
                              :placeholder="parameter.placeholder"
                            />
                          </div>
                        </div>
                      </b-tab>
                    </b-tabs>
                  </b-card>
                  <b-button
                    v-if="userZelid"
                    v-ripple.400="'rgba(255, 255, 255, 0.15)'"
                    variant="outline-warning"
                    aria-label="View Additional Details"
                    class="mb-2"
                    @click="componentParamsModalShowing = true"
                  >
                    View Additional Details
                  </b-button>
                </b-tab>
              </b-tabs>
            </b-card>
          </b-card>
        </b-col>
        <b-col
          xxl="3"
          xl="4"
          lg="4"
          class="d-lg-flex d-none"
        >
          <b-card no-body>
            <b-card-header class="app-requirements-header">
              <h4 class="mb-0">
                CPU
              </h4>
            </b-card-header>
            <vue-apex-charts
              class="mt-1"
              type="radialBar"
              height="200"
              :options="cpuRadialBar"
              :series="cpu.series"
            />
          </b-card>
          <b-card no-body>
            <b-card-header class="app-requirements-header">
              <h4 class="mb-0">
                RAM
              </h4>
            </b-card-header>
            <vue-apex-charts
              class="mt-1"
              type="radialBar"
              height="200"
              :options="ramRadialBar"
              :series="ram.series"
            />
          </b-card>
          <b-card no-body>
            <b-card-header class="app-requirements-header">
              <h4 class="mb-0">
                HDD
              </h4>
            </b-card-header>
            <vue-apex-charts
              class="mt-1"
              type="radialBar"
              height="200"
              :options="hddRadialBar"
              :series="hdd.series"
            />
          </b-card>
        </b-col>
        <b-row class="d-lg-none d-sm-none d-md-flex d-none">
          <b-col md="4">
            <b-card no-body>
              <b-card-header class="app-requirements-header">
                <h5 class="mb-0">
                  CPU
                </h5>
              </b-card-header>
              <vue-apex-charts
                class="mt-1"
                type="radialBar"
                height="200"
                :options="cpuRadialBar"
                :series="cpu.series"
              />
            </b-card>
          </b-col>
          <b-col md="4">
            <b-card no-body>
              <b-card-header class="app-requirements-header">
                <h5 class="mb-0">
                  RAM
                </h5>
              </b-card-header>
              <vue-apex-charts
                class="mt-1"
                type="radialBar"
                height="200"
                :options="ramRadialBar"
                :series="ram.series"
              />
            </b-card>
          </b-col>
          <b-col md="4">
            <b-card no-body>
              <b-card-header class="app-requirements-header">
                <h5 class="mb-0">
                  HDD
                </h5>
              </b-card-header>
              <vue-apex-charts
                class="mt-1"
                type="radialBar"
                height="200"
                :options="hddRadialBar"
                :series="hdd.series"
              />
            </b-card>
          </b-col>
        </b-row>
        <b-row class="d-md-none">
          <b-col cols="4">
            <b-card no-body>
              <b-card-header class="app-requirements-header">
                <h6 class="mb-0">
                  CPU
                </h6>
              </b-card-header>
              <vue-apex-charts
                class="mt-3"
                type="radialBar"
                height="130"
                :options="cpuRadialBarSmall"
                :series="cpu.series"
              />
            </b-card>
          </b-col>
          <b-col cols="4">
            <b-card no-body>
              <b-card-header class="app-requirements-header">
                <h6 class="mb-0">
                  RAM
                </h6>
              </b-card-header>
              <vue-apex-charts
                class="mt-3"
                type="radialBar"
                height="130"
                :options="ramRadialBarSmall"
                :series="ram.series"
              />
            </b-card>
          </b-col>
          <b-col cols="4">
            <b-card no-body>
              <b-card-header class="app-requirements-header">
                <h6 class="mb-0">
                  HDD
                </h6>
              </b-card-header>
              <vue-apex-charts
                class="mt-3"
                type="radialBar"
                height="130"
                :options="hddRadialBarSmall"
                :series="hdd.series"
              />
            </b-card>
          </b-col>
        </b-row>
      </b-row>
      <div
        v-if="!appData.enabled"
        class="text-center"
      >
        <h4>
          This app is temporarily disabled
        </h4>
      </div>
      <div
        v-else
        class="text-center"
      >
        <b-button
          v-if="userZelid"
          v-ripple.400="'rgba(255, 255, 255, 0.15)'"
          variant="success"
          aria-label="Launch Marketplace App"
          class="mb-2"
          @click="checkFluxSpecificationsAndFormatMessage"
        >
          Start Launching Marketplace App
        </b-button>
        <h4 v-else>
          Please login using your ZelID to deploy Marketplace Apps
        </h4>
      </div>
    </vue-perfect-scrollbar>

    <b-modal
      v-model="componentParamsModalShowing"
      title="Extra Component Parameters"
      size="lg"
      centered
      button-size="sm"
      ok-only
      ok-title="Close"
    >
      <div v-if="currentComponent">
        <list-entry
          title="Static Parameters"
          :data="currentComponent.environmentParameters.join(', ')"
        />
        <list-entry
          title="Custom Domains"
          :data="currentComponent.domains.join(', ') || 'none'"
        />
        <list-entry
          title="Automatic Domains"
          :data="constructAutomaticDomains(appData.name).join(', ')"
        />
        <list-entry
          title="Ports"
          :data="currentComponent.ports.join(', ')"
        />
        <list-entry
          title="Container Ports"
          :data="currentComponent.containerPorts.join(', ')"
        />
        <list-entry
          title="Container Data"
          :data="currentComponent.containerData"
        />
        <list-entry
          title="Commands"
          :data="currentComponent.commands.length > 0 ? currentComponent.commands.join(', ') : 'none'"
        />
      </div>
    </b-modal>

    <b-modal
      v-model="confirmLaunchDialogCloseShowing"
      title="Finish Launching App?"
      size="sm"
      centered
      button-size="sm"
      ok-title="Yes"
      cancel-title="No"
      @ok="confirmLaunchDialogCloseShowing = false; launchModalShowing = false;"
    >
      <h3 class="text-center">
        Please ensure that you have paid for your app, or saved the payment details for later.
      </h3>
      <br>
      <h4 class="text-center">
        Close the Launch App dialog?
      </h4>
    </b-modal>

    <b-modal
      v-model="launchModalShowing"
      title="Launching Marketplace App"
      size="xlg"
      centered
      no-close-on-backdrop
      no-close-on-esc
      button-size="sm"
      ok-only
      ok-title="Cancel"
      @ok="confirmLaunchDialogCancel"
    >
      <form-wizard
        :color="tierColors.cumulus"
        :title="null"
        :subtitle="null"
        layout="vertical"
        back-button-text="Previous"
        class="wizard-vertical mb-3"
        @on-complete="confirmLaunchDialogFinish()"
      >
        <tab-content title="Check Registration">
          <b-card
            title="Registration Message"
            class="text-center wizard-card"
          >
            <b-form-textarea
              id="registrationmessage"
              v-model="dataToSign"
              rows="6"
              readonly
            />
          </b-card>
        </tab-content>
        <tab-content
          title="Sign App Message"
          :before-change="() => signature !== null"
        >
          <b-card
            title="Sign App Message with Zelcore"
            class="text-center wizard-card"
          >
            <a
              :href="'zel:?action=sign&message=' + dataToSign + '&icon=https%3A%2F%2Fraw.githubusercontent.com%2Frunonflux%2Fflux%2Fmaster%2FzelID.svg&callback=' + callbackValue()"
              @click="initiateSignWS"
            >
              <img
                class="zelidLogin mb-2"
                src="@/assets/images/zelID.svg"
                alt="Zel ID"
                height="100%"
                width="100%"
              >
            </a>
            <b-form-input
              id="signature"
              v-model="signature"
            />
          </b-card>
        </tab-content>
        <tab-content
          title="Register App"
          :before-change="() => registrationHash !== null"
        >
          <b-card
            title="Register App"
            class="text-center wizard-card"
          >
            <b-card-text>
              Price: {{ appPricePerMonth }} FLUX
            </b-card-text>
            <b-button
              v-ripple.400="'rgba(255, 255, 255, 0.15)'"
              variant="success"
              aria-label="Register Flux App"
              class="my-1"
              :disabled="registrationHash && registrationHash.length > 0"
              @click="register"
            >
              Register Flux App
            </b-button>
            <b-card-text
              v-if="registrationHash"
              v-b-tooltip
              :title="registrationHash"
              class="mt-1"
            >
              Registration Hash received
            </b-card-text>
          </b-card>
        </tab-content>
        <tab-content title="Send Payment">
          <b-row class="match-height">
            <b-col lg="8">
              <b-card
                title="Send Payment"
                class="text-center wizard-card"
              >
                <b-card-text>
                  To finish the application update, please make a transaction of {{ appPricePerMonth }} FLUX to address<br>
                  '{{ deploymentAddress }}'<br>
                  with the following message<br>
                  '{{ registrationHash }}'
                </b-card-text>
                <br>
                The transaction must be mined by {{ new Date(validTill).toLocaleString('en-GB', timeoptions.shortDate) }}
                <br><br>
                The application will be subscribed until {{ new Date(subscribedTill).toLocaleString('en-GB', timeoptions.shortDate) }}
              </b-card>
            </b-col>
            <b-col lg="4">
              <b-card
                title="Pay with Zelcore"
                class="text-center wizard-card"
              >
                <a :href="'zel:?action=pay&coin=zelcash&address=' + deploymentAddress + '&amount=' + appPricePerMonth + '&message=' + registrationHash + '&icon=https%3A%2F%2Fraw.githubusercontent.com%2Frunonflux%2Fflux%2Fmaster%2Fflux_banner.png'">
                  <img
                    class="zelidLogin"
                    src="@/assets/images/zelID.svg"
                    alt="Zel ID"
                    height="100%"
                    width="100%"
                  >
                </a>
              </b-card>
            </b-col>
          </b-row>
        </tab-content>
      </form-wizard>
    </b-modal>
  </div>
</template>

<script>
import {
  BButton,
  BCard,
  BCardHeader,
  BCardText,
  BCol,
  BFormInput,
  BFormTextarea,
  BModal,
  BRow,
  BTabs,
  BTab,
  VBModal,
  VBToggle,
  VBTooltip,
} from 'bootstrap-vue';
import {
  FormWizard,
  TabContent,
} from 'vue-form-wizard';
import VuePerfectScrollbar from 'vue-perfect-scrollbar';
import VueApexCharts from 'vue-apexcharts';
import Ripple from 'vue-ripple-directive';
import { useToast } from 'vue-toastification/composition';
import ToastificationContent from '@core/components/toastification/ToastificationContent.vue';

import { $themeColors } from '@themeConfig';
import 'vue-form-wizard/dist/vue-form-wizard.min.css';

import {
  ref,
  watch,
  computed,
} from '@vue/composition-api';

import ListEntry from '@/views/components/ListEntry.vue';
import AppsService from '@/services/AppsService';
import tierColors from '@/libs/colors';

const qs = require('qs');
const axios = require('axios');
const store = require('store');
const timeoptions = require('@/libs/dateFormat');

export default {
  components: {
    BButton,
    BCard,
    BCardHeader,
    BCardText,
    BCol,
    BFormInput,
    BFormTextarea,
    BModal,
    BRow,
    BTabs,
    BTab,

    FormWizard,
    TabContent,

    // eslint-disable-next-line vue/no-unused-components
    ToastificationContent,
    ListEntry,

    // 3rd Party
    VuePerfectScrollbar,
    VueApexCharts,
  },
  directives: {
    Ripple,
    'b-modal': VBModal,
    'b-toggle': VBToggle,
    'b-tooltip': VBTooltip,
  },
  props: {
    appData: {
      type: Object,
      required: true,
    },
    zelid: {
      type: String,
      required: false,
      default: '',
    },
    tier: {
      type: String,
      required: true,
      default: '',
    },
  },
  setup(props, ctx) {
    // Use toast
    const toast = useToast();

    const resolveTagVariant = (status) => {
      if (status === 'Open') return 'warning';
      if (status === 'Passed') return 'success';
      if (status === 'Unpaid') return 'info';
      if (status && status.startsWith('Rejected')) return 'danger';
      return 'primary';
    };

    const tier = ref('');
    tier.value = props.tier;
    const userZelid = ref('');
    userZelid.value = props.zelid;

    // Variables to control showing dialogs
    const launchModalShowing = ref(false);
    const componentParamsModalShowing = ref(false);
    const confirmLaunchDialogCloseShowing = ref(false);

    // Holds the currently selected component, for viewing
    // additional parameters in a modal dialog
    const currentComponent = ref(null);

    // Registration variables
    const version = ref(1);
    const registrationtype = ref('fluxappregister');
    const dataToSign = ref(null);
    const signature = ref(null);
    const dataForAppRegistration = ref(null);
    const timestamp = ref(null);
    const appPricePerMonth = ref(0);
    const registrationHash = ref(null);
    const websocket = ref(null);

    const config = computed(() => ctx.root.$store.state.flux.config);
    const validTill = computed(() => timestamp.value + 60 * 60 * 1000); // 1 hour
    const subscribedTill = computed(() => timestamp.value + 30 * 24 * 60 * 60 * 1000 + 60 * 60 * 1000); // 1 month

    const callbackValue = () => {
      const { protocol, hostname, port } = window.location;
      let mybackend = '';
      mybackend += protocol;
      mybackend += '//';
      const regex = /[A-Za-z]/g;
      if (hostname.match(regex)) {
        const names = hostname.split('.');
        names[0] = 'api';
        mybackend += names.join('.');
      } else {
        if (typeof hostname === 'string') {
          ctx.root.$store.commit('flux/setUserIp', hostname);
        }
        if (+port > 16100) {
          const apiPort = +port + 1;
          ctx.root.$store.commit('flux/setFluxPort', apiPort);
        }
        mybackend += hostname;
        mybackend += ':';
        mybackend += config.value.apiPort;
      }
      const backendURL = store.get('backendURL') || mybackend;
      const url = `${backendURL}/id/providesign`;
      return encodeURI(url);
    };

    const onError = (evt) => {
      console.log(evt);
    };
    const onMessage = (evt) => {
      const data = qs.parse(evt.data);
      if (data.status === 'success' && data.data) {
        // user is now signed. Store their values
        signature.value = data.data.signature;
      }
      console.log(data);
      console.log(evt);
    };
    const onClose = (evt) => {
      console.log(evt);
    };
    const onOpen = (evt) => {
      console.log(evt);
    };

    const initiateSignWS = () => {
      const { protocol, hostname, port } = window.location;
      let mybackend = '';
      mybackend += protocol;
      mybackend += '//';
      const regex = /[A-Za-z]/g;
      if (hostname.match(regex)) {
        const names = hostname.split('.');
        names[0] = 'api';
        mybackend += names.join('.');
      } else {
        if (typeof hostname === 'string') {
          ctx.root.$store.commit('flux/setUserIp', hostname);
        }
        if (+port > 16100) {
          const apiPort = +port + 1;
          ctx.root.$store.commit('flux/setFluxPort', apiPort);
        }
        mybackend += hostname;
        mybackend += ':';
        mybackend += config.value.apiPort;
      }
      let backendURL = store.get('backendURL') || mybackend;
      backendURL = backendURL.replace('https://', 'wss://');
      backendURL = backendURL.replace('http://', 'ws://');
      const signatureMessage = userZelid.value + timestamp.value;
      console.log(`signatureMessage: ${signatureMessage}`);
      const wsuri = `${backendURL}/ws/sign/${signatureMessage}`;
      const ws = new WebSocket(wsuri);
      websocket.value = ws;

      ws.onopen = (evt) => { onOpen(evt); };
      ws.onclose = (evt) => { onClose(evt); };
      ws.onmessage = (evt) => { onMessage(evt); };
      ws.onerror = (evt) => { onError(evt); };
    };

    const perfectScrollbarSettings = {
      maxScrollbarLength: 150,
    };

    const resolveCpu = (app) => app.compose.reduce((total, component) => total + component.cpu, 0);

    const resolveRam = (app) => app.compose.reduce((total, component) => total + component.ram, 0);

    const resolveHdd = (app) => app.compose.reduce((total, component) => total + component.hdd, 0);

    const cpu = ref({
      series: [],
    });
    const ram = ref({
      series: [],
    });
    const hdd = ref({
      series: [],
    });

    watch(() => props.appData, () => {
      if (websocket.value !== null) {
        websocket.value.close();
        websocket.value = null;
      }
      cpu.value = {
        series: [((resolveCpu(props.appData) / 15) * 100)],
      };
      ram.value = {
        series: [((resolveRam(props.appData) / 59000) * 100)],
      };
      hdd.value = {
        series: [((resolveHdd(props.appData) / 840) * 100)],
      };

      // Evaluate any user parameters from the database
      props.appData.compose.forEach((component) => {
        const paramModel = component.userEnvironmentParameters;
        // check if any of these parameters are special 'port' parameters
        paramModel.forEach((parameter) => {
          if (Object.prototype.hasOwnProperty.call(parameter, 'port')) {
            // eslint-disable-next-line no-param-reassign
            parameter.value = component.ports[parameter.port];
          }
        });
      });

      currentComponent.value = props.appData.compose[0];
    });

    const constructUniqueAppName = (appName) => `${appName}${Date.now()}`;

    const constructAutomaticDomains = (appName) => {
      if (!userZelid.value) {
        return ['No ZelID'];
      }
      const appNameWithTimestamp = constructUniqueAppName(appName);
      const lowerCaseName = appNameWithTimestamp.toLowerCase();
      const domains = [`${lowerCaseName}.app.runonflux.io`];
      return domains;
    };

    const showToast = (variant, title, icon = 'InfoIcon') => {
      toast({
        component: ToastificationContent,
        props: {
          title,
          icon,
          variant,
        },
      });
    };

    const deploymentAddress = ref(null);
    const appsDeploymentInformation = async () => {
      const response = await AppsService.appsDeploymentInformation();
      const { data } = response.data;
      if (response.data.status === 'success') {
        deploymentAddress.value = data.address;
      } else {
        showToast('danger', response.data.data.message || response.data.data);
      }
    };
    appsDeploymentInformation();

    const checkFluxSpecificationsAndFormatMessage = async () => {
      try {
        // construct a valid v4 app spec from the marketplace app spec,
        // filtering out unnecessary fields like 'price' and 'category'
        const appName = constructUniqueAppName(props.appData.name);
        const appSpecification = {
          version: props.appData.version,
          name: appName,
          description: props.appData.description,
          owner: userZelid.value,
          instances: props.appData.instances,
          compose: [],
        };
        if (props.appData.version >= 5) {
          appSpecification.contacts = [];
          appSpecification.geolocation = [];
        }
        if (props.appData.version >= 6) {
          appSpecification.expire = props.appData.expire || 22000;
        }
        if (props.appData.version >= 7) {
          appSpecification.staticip = props.appData.staticip;
          appSpecification.nodes = [];
        }
        // formation, pre verification
        for (let i = 0; i < props.appData.compose.length; i += 1) {
          const component = props.appData.compose[i];
          let envParams = JSON.parse(JSON.stringify(component.environmentParameters));
          component.userEnvironmentParameters.forEach((param) => {
            envParams.push(`${param.name}=${param.value}`);
          });
          if (component.envFluxStorage) {
            const envid = Math.floor((Math.random() * 999999999999999)).toString();
            const data = {
              envid,
              env: envParams,
            };
            // eslint-disable-next-line no-await-in-loop
            const resp = await axios.post('https://storage.runonflux.io/v1/env', data);
            if (resp.data.status === 'error') {
              throw new Error(resp.data.message || resp.data);
            }
            showToast('success', 'Successful upload of Environment Parameters to Flux Storage');
            envParams = [`F_S_ENV=https://storage.runonflux.io/v1/env/${envid}`];
          }
          const appComponent = {
            name: component.name,
            description: component.description,
            repotag: component.repotag,
            ports: component.ports,
            containerPorts: component.containerPorts,
            environmentParameters: envParams,
            commands: component.commands,
            containerData: component.containerData,
            domains: component.domains,
            cpu: component.cpu,
            ram: component.ram,
            hdd: component.hdd,
            tiered: component.tiered,
          };
          if (component.tiered) {
            appComponent.cpubasic = component.cpubasic;
            appComponent.rambasic = component.rambasic;
            appComponent.hddbasic = component.hddbasic;
            appComponent.cpusuper = component.cpusuper;
            appComponent.ramsuper = component.ramsuper;
            appComponent.hddsuper = component.hddsuper;
            appComponent.cpubamf = component.cpubamf;
            appComponent.rambamf = component.rambamf;
            appComponent.hddbamf = component.hddbamf;
          }
          if (props.appData.version >= 7) {
            appComponent.secrets = '';
            appComponent.repoauth = '';
          }
          appSpecification.compose.push(appComponent);
        }

        // call api for verification of app registration specifications that returns formatted specs
        const responseAppSpecs = await AppsService.appRegistrationVerificaiton(appSpecification);
        console.log(responseAppSpecs);
        if (responseAppSpecs.data.status === 'error') {
          throw new Error(responseAppSpecs.data.data.message || responseAppSpecs.data.data);
        }
        const appSpecFormatted = responseAppSpecs.data.data;
        const response = await AppsService.appPrice(appSpecFormatted);
        if (response.data.status === 'error') {
          throw new Error(response.data.data.message || response.data.data);
        }
        if (response.data.data > props.appData.price) {
          throw new Error('Marketplace App Price is too low');
        }
        if (websocket.value !== null) {
          websocket.value.close();
          websocket.value = null;
        }
        timestamp.value = new Date().getTime();
        dataForAppRegistration.value = appSpecFormatted;
        appPricePerMonth.value = props.appData.price;
        dataToSign.value = `${registrationtype.value}${version.value}${JSON.stringify(appSpecFormatted)}${new Date().getTime()}`;
        registrationHash.value = null;
        signature.value = null;
        launchModalShowing.value = true;
      } catch (error) {
        console.log(error);
        showToast('danger', error.message || error);
      }
    };

    const smallchart = {
      height: 100,
      type: 'radialBar',
      sparkline: {
        enabled: true,
      },
      dropShadow: {
        enabled: true,
        blur: 3,
        left: 1,
        top: 1,
        opacity: 0.1,
      },
    };

    const largechart = {
      height: 200,
      type: 'radialBar',
      sparkline: {
        enabled: true,
      },
      dropShadow: {
        enabled: true,
        blur: 3,
        left: 1,
        top: 1,
        opacity: 0.1,
      },
    };

    const cpuRadialBar = {
      chart: largechart,
      colors: [$themeColors.primary],
      labels: ['Cores'],
      plotOptions: {
        radialBar: {
          offsetY: -10,
          startAngle: -150,
          endAngle: 150,
          hollow: {
            size: '77%',
          },
          track: {
            background: $themeColors.dark,
            strokeWidth: '50%',
          },
          dataLabels: {
            name: {
              offsetY: -15,
              color: $themeColors.light,
              fontSize: '1.5rem',
            },
            value: {
              formatter: (val) => ((parseFloat(val) * 15) / 100).toFixed(1),
              offsetY: 10,
              color: $themeColors.light,
              fontSize: '2.86rem',
              fontWeight: '600',
            },
          },
        },
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'dark',
          type: 'horizontal',
          shadeIntensity: 0.5,
          gradientToColors: [$themeColors.success],
          inverseColors: true,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 100],
        },
      },
      stroke: {
        lineCap: 'round',
      },
      grid: {
        padding: {
          bottom: 30,
        },
      },
    };

    const cpuRadialBarSmall = {
      chart: smallchart,
      colors: [$themeColors.primary],
      labels: ['Cores'],
      plotOptions: {
        radialBar: {
          offsetY: -10,
          startAngle: -150,
          endAngle: 150,
          hollow: {
            size: '70%',
          },
          track: {
            background: $themeColors.dark,
            strokeWidth: '50%',
          },
          dataLabels: {
            name: {
              offsetY: -15,
              color: $themeColors.light,
              fontSize: '1.2rem',
            },
            value: {
              formatter: (val) => ((parseFloat(val) * 15) / 100).toFixed(1),
              offsetY: 10,
              color: $themeColors.light,
              fontSize: '2rem',
              fontWeight: '400',
            },
          },
        },
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'dark',
          type: 'horizontal',
          shadeIntensity: 0.5,
          gradientToColors: [$themeColors.success],
          inverseColors: true,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 100],
        },
      },
      stroke: {
        lineCap: 'round',
      },
      grid: {
        padding: {
          bottom: 10,
        },
      },
    };

    const ramRadialBar = {
      chart: largechart,
      colors: [$themeColors.primary],
      labels: ['MB'],
      plotOptions: {
        radialBar: {
          offsetY: -10,
          startAngle: -150,
          endAngle: 150,
          hollow: {
            size: '77%',
          },
          track: {
            background: $themeColors.dark,
            strokeWidth: '50%',
          },
          dataLabels: {
            name: {
              offsetY: -15,
              color: $themeColors.light,
              fontSize: '1.5rem',
            },
            value: {
              formatter: (val) => ((parseFloat(val) * 59000) / 100).toFixed(0),
              offsetY: 10,
              color: $themeColors.light,
              fontSize: '2.86rem',
              fontWeight: '600',
            },
          },
        },
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'dark',
          type: 'horizontal',
          shadeIntensity: 0.5,
          gradientToColors: [$themeColors.success],
          inverseColors: true,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 100],
        },
      },
      stroke: {
        lineCap: 'round',
      },
      grid: {
        padding: {
          bottom: 30,
        },
      },
    };

    const ramRadialBarSmall = {
      chart: smallchart,
      colors: [$themeColors.primary],
      labels: ['MB'],
      plotOptions: {
        radialBar: {
          offsetY: -10,
          startAngle: -150,
          endAngle: 150,
          hollow: {
            size: '70%',
          },
          track: {
            background: $themeColors.dark,
            strokeWidth: '50%',
          },
          dataLabels: {
            name: {
              offsetY: -15,
              color: $themeColors.light,
              fontSize: '1.2rem',
            },
            value: {
              formatter: (val) => ((parseFloat(val) * 59000) / 100).toFixed(0),
              offsetY: 10,
              color: $themeColors.light,
              fontSize: '2rem',
              fontWeight: '400',
            },
          },
        },
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'dark',
          type: 'horizontal',
          shadeIntensity: 0.5,
          gradientToColors: [$themeColors.success],
          inverseColors: true,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 100],
        },
      },
      stroke: {
        lineCap: 'round',
      },
      grid: {
        padding: {
          bottom: 10,
        },
      },
    };

    const hddRadialBar = {
      chart: largechart,
      colors: [$themeColors.primary],
      labels: ['GB'],
      plotOptions: {
        radialBar: {
          offsetY: -10,
          startAngle: -150,
          endAngle: 150,
          hollow: {
            size: '77%',
          },
          track: {
            background: $themeColors.dark,
            strokeWidth: '50%',
          },
          dataLabels: {
            name: {
              offsetY: -15,
              color: $themeColors.light,
              fontSize: '1.5rem',
            },
            value: {
              formatter: (val) => ((parseFloat(val) * 840) / 100).toFixed(0),
              offsetY: 10,
              color: $themeColors.light,
              fontSize: '2.86rem',
              fontWeight: '600',
            },
          },
        },
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'dark',
          type: 'horizontal',
          shadeIntensity: 0.5,
          gradientToColors: [$themeColors.success],
          inverseColors: true,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 100],
        },
      },
      stroke: {
        lineCap: 'round',
      },
      grid: {
        padding: {
          bottom: 30,
        },
      },
    };

    const hddRadialBarSmall = {
      chart: smallchart,
      colors: [$themeColors.primary],
      labels: ['GB'],
      plotOptions: {
        radialBar: {
          offsetY: -10,
          startAngle: -150,
          endAngle: 150,
          hollow: {
            size: '70%',
          },
          track: {
            background: $themeColors.dark,
            strokeWidth: '50%',
          },
          dataLabels: {
            name: {
              offsetY: -15,
              color: $themeColors.light,
              fontSize: '1.2rem',
            },
            value: {
              formatter: (val) => ((parseFloat(val) * 840) / 100).toFixed(0),
              offsetY: 10,
              color: $themeColors.light,
              fontSize: '2rem',
              fontWeight: '400',
            },
          },
        },
      },
      fill: {
        type: 'gradient',
        gradient: {
          shade: 'dark',
          type: 'horizontal',
          shadeIntensity: 0.5,
          gradientToColors: [$themeColors.success],
          inverseColors: true,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 100],
        },
      },
      stroke: {
        lineCap: 'round',
      },
      grid: {
        padding: {
          bottom: 10,
        },
      },
    };

    const register = async () => {
      const zelidauth = localStorage.getItem('zelidauth');
      const data = {
        type: registrationtype.value,
        version: version.value,
        appSpecification: dataForAppRegistration.value,
        timestamp: timestamp.value,
        signature: signature.value,
      };
      showToast('info', 'Propagating message accross Flux network...');
      const response = await AppsService.registerApp(zelidauth, data).catch((error) => {
        showToast('danger', error.message || error);
      });
      console.log(response);
      if (response.data.status === 'success') {
        registrationHash.value = response.data.data;
        showToast('success', response.data.data.message || response.data.data);
      } else {
        showToast('danger', response.data.data.message || response.data.data);
      }
    };

    const componentSelected = (component) => {
      currentComponent.value = props.appData.compose[component];
    };

    const confirmLaunchDialogFinish = () => {
      confirmLaunchDialogCloseShowing.value = true;
    };

    const confirmLaunchDialogCancel = (modalEvt) => {
      if (registrationHash.value !== null) {
        modalEvt.preventDefault();
        confirmLaunchDialogCloseShowing.value = true;
      }
    };

    return {

      // UI
      perfectScrollbarSettings,
      resolveTagVariant,

      resolveCpu,
      resolveRam,
      resolveHdd,

      constructAutomaticDomains,
      checkFluxSpecificationsAndFormatMessage,

      timeoptions,

      cpuRadialBar,
      cpuRadialBarSmall,
      cpu,

      ramRadialBar,
      ramRadialBarSmall,
      ram,

      hddRadialBar,
      hddRadialBarSmall,
      hdd,

      userZelid,
      dataToSign,
      signature,
      appPricePerMonth,
      registrationHash,
      deploymentAddress,

      validTill,
      subscribedTill,

      register,
      callbackValue,
      initiateSignWS,

      launchModalShowing,
      componentParamsModalShowing,
      confirmLaunchDialogCloseShowing,
      confirmLaunchDialogFinish,
      confirmLaunchDialogCancel,

      currentComponent,
      componentSelected,

      tierColors,
    };
  },
};
</script>

<style scoped>
.inline {
  display: inline;
  padding-left: 5px;
}
.zelidLogin {
  height: 100px;
}
.zelidLogin img {
  -webkit-app-region: no-drag;
  transition: 0.1s;
}

a img {
  transition: all 0.05s ease-in-out;
}

a:hover img {
  filter: opacity(70%);
  transform: scale(1.1);
}
.text-decoration-line-through {
  text-decoration: line-through;
}
.wizard-card {
  height: 250px;
}
</style>
<style lang="scss">
@import "@core/scss/vue/libs/vue-wizard.scss";
</style>
