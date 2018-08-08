<template>
  <b-card :header="caption">
    <b-table :hover="hover" :bordered="bordered" :small="small" :fixed="fixed" responsive="sm" :items="items" :fields="fields" :current-page="currentPage" :per-page="perPage">
      <template slot="description" slot-scope="data">
        <div class="clearfix">
          <div class="float-left">
            {{data.item.description}}
          </div>
          <div class="float-right">
            <b-btn class="badge badge-pill badge-dark" @click="toggleJustification(data.item)">
              {{$t("message.why_question")}}
            </b-btn>
          </div>
        </div>
        <b-collapse v-model="data.item.showJustification" class="card card-body clearfix" id="prueba">
          <h5 class="float-right">{{$t("message.justification")}}</h5>
          Anim pariatur cliche reprehenderit, enim eiusmod high life accusamus terry richardson ad squid. Nihil anim keffiyeh helvetica, craft beer labore wes anderson cred nesciunt sapiente ea proident.
          <small class="text-muted">
           {{data.item.justification}}
          </small>
        </b-collapse>
      </template>
      <template slot="status" slot-scope="data">
        <b-badge :variant="getBadge(data.item.status)">{{data.item.status}}</b-badge>
      </template>
      <template slot="responseFormat" slot-scope="data">
      <div>
         <!-- <b-form-group :id="'likertScale'+data.item.itemId">
          <b-form-radio-group v-model="selected" :options="options" name="likertScale">
          </b-form-radio-group>
        </b-form-group> -->
        <b-form-radio-group   name="radioSubComponent" :id="'likertScale'+data.item.itemId">
          <div  v-for="(value, key) in options" :key="key" class="custom-control-inline col-sm-3 py-0">
            <b-form-radio :value="value.value" :id="data.item.itemId+value.value" >
              <small>{{value.text}}</small>
              <b-tooltip  :target="data.item.itemId+value.value" placement="top">
                Hello <strong>World!</strong>
              </b-tooltip>
            </b-form-radio>

          </div>
        </b-form-radio-group>
      </div>
      </template>
    </b-table>
    <nav>
      <b-pagination :total-rows="getRowCount(items)" :per-page="perPage" v-model="currentPage" prev-text="Prev" next-text="Next" hide-goto-end-buttons/>
    </nav>
  </b-card>
</template>

<script>

// import BDData from '../at-views/_BDData.js'
/**
   * Randomize array element order in-place.
   * Using Durstenfeld shuffle algorithm.
   */
const shuffleArray = (array) => {
  for (let i = array.length - 1; i > 0; i--) {
    let j = Math.floor(Math.random() * (i + 1))
    let temp = array[i]
    array[i] = array[j]
    array[j] = temp
  }
  return array
}

export default {
  name: 'c-table',
  props: {
    caption: {
      type: String,
      default: 'Table'
    },
    hover: {
      type: Boolean,
      default: false
    },
    striped: {
      type: Boolean,
      default: false
    },
    bordered: {
      type: Boolean,
      default: false
    },
    small: {
      type: Boolean,
      default: false
    },
    fixed: {
      type: Boolean,
      default: false
    }
  },
  data: () => {
    return {
      showDismissibleAlert: false,
      items3: shuffleArray([
        {username: 'Samppa Nori', registered: '2012/01/01', role: 'Member', status: 'Active', responseFormat: this.options, id: 2},
        {username: 'Estavan Lykos', registered: '2012/02/01', role: 'Staff', status: 'Banned', responseFormat: this.options, id: 3},
        {username: 'Chetan Mohamed', registered: '2012/02/01', role: 'Admin', status: 'Inactive'},
        {username: 'Derick Maximinus', registered: '2012/03/01', role: 'Member', status: 'Pending'},
        {username: 'Friderik Dávid', registered: '2012/01/21', role: 'Staff', status: 'Active'},
        {username: 'Yiorgos Avraamu', registered: '2012/01/01', role: 'Member', status: 'Active'},
        {username: 'Avram Tarasios', registered: '2012/02/01', role: 'Staff', status: 'Banned'},
        {username: 'Quintin Ed', registered: '2012/02/01', role: 'Admin', status: 'Inactive'},
        {username: 'Enéas Kwadwo', registered: '2012/03/01', role: 'Member', status: 'Pending'},
        {username: 'Agapetus Tadeáš', registered: '2012/01/21', role: 'Staff', status: 'Active'},
        {username: 'Carwyn Fachtna', registered: '2012/01/01', role: 'Member', status: 'Active'},
        {username: 'Nehemiah Tatius', registered: '2012/02/01', role: 'Staff', status: 'Banned'},
        {username: 'Ebbe Gemariah', registered: '2012/02/01', role: 'Admin', status: 'Inactive'},
        {username: 'Eustorgios Amulius', registered: '2012/03/01', role: 'Member', status: 'Pending'},
        {username: 'Leopold Gáspár', registered: '2012/01/21', role: 'Staff', status: 'Active'},
        {username: 'Pompeius René', registered: '2012/01/01', role: 'Member', status: 'Active'},
        {username: 'Paĉjo Jadon', registered: '2012/02/01', role: 'Staff', status: 'Banned'},
        {username: 'Micheal Mercurius', registered: '2012/02/01', role: 'Admin', status: 'Inactive'},
        {username: 'Ganesha Dubhghall', registered: '2012/03/01', role: 'Member', status: 'Pending'},
        {username: 'Hiroto Šimun', registered: '2012/01/21', role: 'Staff', status: 'Active'},
        {username: 'Vishnu Serghei', registered: '2012/01/01', role: 'Member', status: 'Active'},
        {username: 'Zbyněk Phoibos', registered: '2012/02/01', role: 'Staff', status: 'Banned'},
        {username: 'Einar Randall', registered: '2012/02/01', role: 'Admin', status: 'Inactive'},
        {username: 'Félix Troels', registered: '2012/03/21', role: 'Staff', status: 'Active'},
        {username: 'Aulus Agmundr', registered: '2012/01/01', role: 'Member', status: 'Pending'}
      ]),
      // items: BDData.customizedInstrument.itemsHierarchy.motivationHierarchy.hierarchicalItem.subHierarchicalItems.subItems
      items: [ {
        'itemId': 'subc1',
        'suggestedImportance': {
          'numericValue': 1,
          'label': 'desirable'
        },
        'responseFormat': 'rating',
        'name': 'Manager support the innitiative',
        'description': 'Managers will support the initiative for exploring a product line solution',
        'justification': 'string',
        'showJustification': false,
        'hierarchicalLevel': 3,
        'feedback': [{
          'feedbackType': 'positive',
          'text': 'string',
          'minScore': 0,
          'maxScore': 0,
          'references': [{
            'name': 'Empirical evaluation of a decision support model for adopting software product line engineering',
            'reference': '(Tüzün, Tekinerdogan, Kalender, & Bilgen, 2015)',
            'cite': 'Tüzün, E., Tekinerdogan, B., Kalender, M. E., & Bilgen, S. (2015). Empirical evaluation of a decision support model for adopting software product line engineering. Information and Software Technology, 60, 77–101. https://doi.org/10.1016/j.infsof.2014.12.007'
          }]
        }]
      },
      {
        'itemId': 'subc2',
        'suggestedImportance': {
          'numericValue': 1,
          'label': 'desirable'
        },
        'responseFormat': 'rating',
        'name': 'Project leader',
        'description': 'The product line project would have a project leader with authority to take decisions and support the idea of change',
        'justification': 'This is the explanation about why one of those criteria are important',
        'showJustification': false,
        'hierarchicalLevel': 3,
        'feedback': [{
          'feedbackType': 'positive',
          'text': 'string',
          'minScore': 0,
          'maxScore': 0,
          'references': [{
            'name': 'Empirical evaluation of a decision support model for adopting software product line engineering',
            'reference': '(Tüzün, Tekinerdogan, Kalender, & Bilgen, 2015)',
            'cite': 'Tüzün, E., Tekinerdogan, B., Kalender, M. E., & Bilgen, S. (2015). Empirical evaluation of a decision support model for adopting software product line engineering. Information and Software Technology, 60, 77–101. https://doi.org/10.1016/j.infsof.2014.12.007'
          }]
        }]
      },
      {
        'itemId': 'subc3',
        'suggestedImportance': {
          'numericValue': 1,
          'label': 'desirable'
        },
        'responseFormat': 'rating',
        'name': 'sub criterion 3',
        'description': 'description sub criterion 3',
        'justification': 'string',
        'showJustification': true,
        'hierarchicalLevel': 3,
        'feedback': [{
          'feedbackType': 'positive',
          'text': 'string',
          'minScore': 0,
          'maxScore': 0,
          'references': [{
            'name': 'Empirical evaluation of a decision support model for adopting software product line engineering',
            'reference': '(Tüzün, Tekinerdogan, Kalender, & Bilgen, 2015)',
            'cite': 'Tüzün, E., Tekinerdogan, B., Kalender, M. E., & Bilgen, S. (2015). Empirical evaluation of a decision support model for adopting software product line engineering. Information and Software Technology, 60, 77–101. https://doi.org/10.1016/j.infsof.2014.12.007'
          }]
        }]
      }
      ],
      fields: [
        {key: 'itemId', label: 'Id', sortable: true},
        {key: 'name', sortable: true},
        {key: 'description'},
        {key: 'responseFormat', label: 'Response'}
      ],
      /* fields: [
        {key: 'username', sortable: true},
        {key: 'registered'},
        {key: 'role'},
        {key: 'status'},
        {key: 'responseFormat'}
      ], */
      currentPage: 1,
      perPage: 5,
      totalRows: 0,
      options: [
        { text: 'Not at all', value: 0 },
        { text: 'Just a litte', value: 1 },
        { text: 'Somewhat', value: 2 },
        { text: 'Mostly', value: 3 },
        { text: 'Almost totally', value: 4 },
        { text: 'Totally', value: 5 },
        { text: 'Don\'t know', value: 0 },
        { text: 'N/A', value: -1 }
      ]
    }
  },
  methods: {
    getBadge (status) {
      return status === 'Active' ? 'success'
        : status === 'Inactive' ? 'secondary'
          : status === 'Pending' ? 'warning'
            : status === 'Banned' ? 'danger' : 'primary'
    },
    getRowCount (items) {
      return items.length
    },
    toggleJustification (item) {
      /* Chages the justification of an item from visible to visible and vice-versa */
      item.showJustification = !item.showJustification
    }
  }
}
</script>
