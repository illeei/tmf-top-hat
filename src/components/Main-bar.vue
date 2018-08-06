<template lang="html">
  <div>
    <nav>
      <div class="nav-links">
        <div class="logo">
          <img class="full-logo" src="../assets/motley-fool-premium.png" alt="Motley Fool Premium">
          <img class="min-logo" src="../assets/motley-fool-premium-logo.png" alt="Motley Fool Premium">
        </div>
        <div class="search">
          <searchBox
            v-model="searchQuery"
            :options="searchOptions"
            :onItemSelected="onSearchItemSelected"
            :onInputChange="onInputChange">
            <div slot="item" slot-scope="props" class="single-item">
              <p class="searchResults" @click="goToCompany(props.item.InstrumentId)"><span id="sym">{{ props.item.Symbol }}</span> <span id="compName">{{ props.item.Name }}</span> <span id="exchange" :class="[ 'exchFlag', props.item.Exchange.toLowerCase() ]">{{ props.item.Exchange }} </span></p>
            </div>
          </searchBox>
        </div>
        <div class="help">
          <i class="far fa-question-circle"></i><p class="icon-label"><a href="https://www.fool.com/contact/customer-service/">Help</a></p>
        </div>
        <div class="dropdown-container">
          <button class="account">
          <i class="far fa-user"></i><p class="icon-label">My Fool</p>
          </button>
          <div class="account-contents">
            <h3>Hi, {{ uname }}!</h3>
            <h6>Collections</h6>
            <ul>
              <li><a href="https://www.fool.com/premium/watchlist/" target="_blank"><i class="far fa-heart"></i>My Stock Watchlist</a></li>
              <li><a href="https://scorecard.fool.com/1081/scorecard/#/dashboard" target="_blank"><i class="fas fa-th"></i>My Scorecard Holdings</a></li>
              <li><a href="https://www.fool.com/premium/boards/" target="_blank"><i class="far fa-comments"></i>My Favorite Boards</a></li>
              <li><a href="https://caps.fool.com/MyPlayer.aspx" target="_blank"><i class="far fa-thumbs-up"></i>My CAPS</a></li>
            </ul>
            <h6>Settings</h6>
            <ul>
              <li><a href="https://www.fool.com/Account/Index.aspx" target="_blank"><i class="fas fa-cog"></i>Account Settings</a></li>
              <li><a href="https://www.fool.com/Account/EmailSettings.aspx" target="_blank"><i class="far fa-envelope"></i>Email Settings</a></li>
              <li><a href="https://www.fool.com/logout/"><i class="fas fa-sign-out-alt"></i>Logout</a></li>
            </ul>
          </div>
        </div>
        <div class="dropdown-container">
          <button class="services">
          <i class="fas fa-bars"></i><p class="icon-label">My Services</p>
          </button>
          <div class="services-contents">
            <h6 class="collapsible"  @click="servicesActive=!servicesActive">Premium Services / Portfolio <i class="fas fa-chevron-down"  :class="{'icon-turn': servicesActive}"></i></h6>
            <div class="subcontents" :class="{active: servicesActive}" >
              <ul>
                <li v-for="service in serviceList"><a :href="service.home_url" target="_blank"><div :class=" ['sq', service.abbreviation] "> </div> {{ service.short_name }} </a></li>
              </ul>
            </div>
            <h6 class="collapsible" @click="reportsActive=!reportsActive">Latest Reports <i class="fas fa-chevron-down" :class="{'icon-turn': reportsActive}"></i></h6>
            <div class="subcontents" :class="{active: reportsActive}">
              <ul>
                <li v-for="report in reportList"><a :href="'https://www.fool.com' + report.root_path" target="_blank"> {{ report.short_name.length >= 30? report.short_name.replace(/^(.{30}[^\s]*).*/, "$1") + ' ...' : report.short_name }} </a></li>
                <li><a href="https://www.fool.com/premium/reports/" target="_blank" style="font-style: italic; text-decoration: underline; "><strong>See More Reports ...</strong></a></li>
              </ul>
            </div>
            <h6 class="collapsible" @click="internationalActive=!internationalActive">International Sites <i class="fas fa-chevron-down" :class="{'icon-turn': internationalActive}"></i></h6>
            <div class="subcontents" :class="{active: internationalActive}">
              <ul>
                <li><a href="https://www.fool.com/"><img src="https://g.foolcdn.com/Art/Flags/18/UnitedStates.png" class="flag" alt="Fool US">Fool - US</a></li>
                <li><a href="https://www.fool.co.uk/" target="_blank"><img src="https://g.foolcdn.com/Art/Flags/18/UnitedKingdom.png" class="flag" alt="Fool UK">Fool - United Kingdom</a></li>
                <li><a href="https://www.fool.com.au/" target="_blank"><img src="https://g.foolcdn.com/Art/Flags/18/Australia.png" class="flag" alt="Fool AU">Fool - Australia</a></li>
                <li><a href="https://www.fool.ca/" target="_blank"><img src="https://g.foolcdn.com/Art/Flags/18/Canada.png" class="flag" alt="Fool CA">Fool - Canada</a></li>
                <li><a href="https://www.fool.sg/" target="_blank"><img src="https://g.foolcdn.com/Art/Flags/18/Singapore.png" class="flag" alt="Fool SG">Fool - Singapore</a></li>
                <li><a href="https://www.fool.de/" target="_blank"><img src="https://g.foolcdn.com/Art/Flags/18/Germany.png" class="flag" alt="Fool DE">Fool - Germany</a></li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
import SearchBox from './SearchBox.vue'

export default {
  props: ['serviceList', 'reportList', 'uname', 'uid'],
  data () {
    return {
      reportsActive: false,
      servicesActive: false,
      internationalActive: false,
      service: '',
      searchQuery: '',
      selectedSearchItem: null,
      options: {},
      searchOptions: {}
    }
  },
  components: {
    searchBox: SearchBox
  },
  methods: {
    onInputChange (query) {
      if (query.trim().length === 0) {
        return null
      }
      const url = `/quotes/v3/instruments/search/?maxResults=10&domain=fool.com&apiKey=public&query=${query}`
      return new Promise(resolve => {
        this.axios.get(url).then(response => {
          const items = []
          response.data.SearchResults.forEach((item) => {
              items.push(item)
          })
          resolve(items)
        })
      })
    },
    onSearchItemSelected (item) {
      this.selectedSearchItem = item
    },
    goToCompany(instrumentId) {
      document.location = `//www.fool.com/premium/company/${instrumentId}`;
    }
  }
}
</script>

<style scoped>
  nav {
    background-image: linear-gradient(180deg, #222 0%, #333 100%);
  }

  a {
    font-size: 14px;
  }

  .nav-links {
    max-width: 1280px;
    color: #fff;
    display: grid;
    grid-template-columns: 2fr 1fr 1fr 1fr 1fr;
    align-items: center;
    justify-items: center;
    min-height: 50px;
    margin: 0 auto;
  }

  .nav-links a{
    text-decoration: none;
    color: #fff;
  }

  .logo {
    justify-self: start;
    padding-left: 30px;
  }

  .full-logo {
    display: none;
    vertical-align: middle;
  }

  .min-logo {
    max-width: 30px;
    vertical-align: middle;
  }

  .icon-label {
    display: none;
  }

  button:hover {
    background: #FFCC33;
    color: #000;
  }

  .search {
    /* nested grid */
    display: grid;
    grid-template-columns: 1fr 2fr;
    max-width: 200px;
    justify-items: center;
    align-items: center;
  }

  .searchResults {
    display: flex;
    align-items: center;
  }

  #sym {
    width: 70px;
    padding: 0 5px;
    font-weight: bold;
    color: #383838;
  }

  #compName {
    width: 150px;
    padding: 0 20px;
  }

  #exchange {
    width: 150px;
    margin-right: 10px;
  }

  button {
    border: none;
    outline: none;
    color: white;
    background: none;
    font-size: 14px;
    width: 100%;
    height: 100%;
  }

  .dropdown-container {
    width: 100%;
    height: 100%;
  }

  .account-contents, .services-contents {
    display: none;
    position: absolute;
    min-width: 280px;
    box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
    z-index: 1;
    right: 0;
    padding: 0;
  }

  .account-contents ul {
    padding: 0;
    margin: 0;
  }

  .account:hover ~ .account-contents,
  .services:hover ~ .services-contents {
    display: block;
  }

  .account-contents:hover,
  .services-contents:hover {
    display: block;
  }

  .account-contents a,
  .services-contents a {
    float: none;
    color: black;
    padding: 8px 16px;
    text-decoration: none;
    display: block;
    text-align: left;
    position: relative;
  }

  .account-contents li,
  .services-contents li {
    color: black;
    text-decoration: none;
    background-color: #fff;
    display: block;
    text-align: left;
    position: relative;
    width: 100%;
    margin: 0;
  }

  .dropdown-container:hover .account,
  .dropdown-container:hover .services {
    background-color: #FFCC33;
    color: #000;
  }

  .account-contents li:hover,
  .services-contents li:hover,
  .account-contents a:hover,
  .services-contents a:hover {
    background-color: #FFCC33;
  }

  .subcontents {
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.2s ease-out;
    padding: 0;
    margin: 0;
  }

  .subcontents ul,
  .account-content ul {
    width: 100%;
    padding: 0;
    margin: 0;
  }

  .active {
    max-height: 100%;
  }

  h3 {
    color: #000;
    text-align: left;
    background-color: #F2F2F2;
    margin: 0;
    padding: 1em;
    font-weight: normal;
    font-style: italic;
  }

  .account-contents hr {
    padding: 0;
    margin: 0;
  }

  h6 {
    color: #000;
    font-weight: bold;
    padding: 10px 16px;
    text-transform: uppercase;
    margin: 0;
    text-align: left;
    background-color: #F2F2F2;
  }

  h6 i {
    float: right;
  }

  .icon-turn {
    transform: rotate(180deg);
  }

  .flag {
    margin-right: 20px;
  }

  i {
    margin-right: 1em;
  }

  /* Flags for exchanges in serach suggestions */

  .exchFlag {
    background-repeat: no-repeat;
    background-position: right;
  }

  .nasdaq, .nyse, .nysemkt, .nasdaqoth, .nasdaqmutfund {
    background-image: url('https://g.foolcdn.com/Art/Flags/18/UnitedStates.png');
  }

  .tsx, .tsxv {
    background-image: url('https://g.foolcdn.com/Art/Flags/18/Canada.png');
  }

  .lse {
    background-image: url('https://g.foolcdn.com/Art/Flags/18/UnitedKingdom.png')
  }

  /* Service Icons */

  .sq {
    height: 15px;
    width: 15px;
    border-radius: 4px;
    display: inline-block;
    vertical-align: middle;
    margin-right: 10px;
  }

  .ryr {
    background: linear-gradient(#6184b3, #3d6190);
  }

  .rb {
    background: linear-gradient(#f16f25, #d45300);
  }

  .sa {
    background: linear-gradient(#1ea0d1, #0080b2);
  }

  .pro {
    background: linear-gradient(#1fa192, #007e70);
  }

  .one {
    background: linear-gradient(#41241c, #191816);
  }

  .opt {
    background: linear-gradient(#257282, #004f60);
  }

  .sn {
    background: linear-gradient(#1e4b7c, #0c1620);
  }

  .exp1 {
    background: linear-gradient(#1c3664, #001331);
  }

  .ti {
    background: linear-gradient(#0099ff, #0385db);
  }

  .pp {
    background: linear-gradient(#660000, #802121);
  }

  .mp {
    background: linear-gradient(#330000, #5b0a0a);
  }

  .ep {
    background: linear-gradient(#5f4638, #37261d);
  }

  .cry {
    background: linear-gradient(#a13a4e, #a45a7b);
  }

  .rs {
    background: linear-gradient(#2E252F, #210e23);
  }

  .hrs {
    background: linear-gradient(#e7c020, #b59630);
  }

  .par {
    background: linear-gradient(#36150E, #1C0F0C);
  }

  @media (min-width: 1000px) {
    nav {
      grid-template-columns: 4fr 1fr 1fr 1fr 1fr;
    }

    .icon-label {
      display: inline-block;
    }

    .help {
      grid-row: auto;
      grid-column: auto;
    }

    .search {
      grid-row: auto;
      grid-column: auto;
    }

    .full-logo {
      display: inline;
      max-width: 300px;
    }

    .min-logo {
      display: none;
    }

    .services {
      white-space:nowrap;
    }

    .services-contents {
      left: auto;
    }

    .account-contents {
      right: auto;
    }
  }

  @media (min-width: 1290px) {
    .services-contents {
      right: auto;
    }
  }
</style>
