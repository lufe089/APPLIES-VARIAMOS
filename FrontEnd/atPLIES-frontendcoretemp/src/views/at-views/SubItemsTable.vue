<template>
  <b-card :header='caption' class="card-body text-center">
    <!-- stacked -->
    <b-table :hover='hover' :bordered='bordered' :small='small'  responsive='md' :items='items' :fields='fields' :current-page='currentPage' :per-page='perPage' caption-top >
      <template slot="table-caption">
      <b-card :no-body="true">
        <b-card-body class="p-3 clearfix">
          <div class='float-right'>
          <i class="fa fa-battery-2"></i>
          <span class="title">Progress</span>
          <span class="value">191,235 <span class="text-muted small">(56%)</span></span>
          <div class="bars">
            <b-progress class="progress-xs" :value="56" variant="info"></b-progress>
          </div>
         </div>
          <!--
          <div class="h1 text-muted text-left mb-4">
            <i class="fa fa-puzzle-piece fa-circle bg-primary p-4 px-5 font-2xl mr-3 float-left round" id='justification_item'></i>
          </div>-->
          <div class="h5 text-primary mb-0 mt-2 text-uppercase">algoooo - items</div>
          <div class='clearfix'>
            <div class='float-left text-muted font-weight-bold font-xs'>
              alhop-4 px-5 font-2xl mr-3
            </div>
            <div class='float-right'>
              <!-- we use @click.stop here to prevent emitting of a 'row-clicked' event  -->
              <!-- https://bootstrap-vue.js.org/docs/components/table/#row-details-support -->
              <b-btn class='badge badge-pill badge-dark'>
              algo
              </b-btn>
            </div>
          </div>
          <div class="text-muted font-weight-bold font-xs">
            <small>
             alhop-4 px-5 font-2xl mr-3
            </small>
          </div>
        </b-card-body>
      </b-card>

      </template>
      <template slot='description' slot-scope='row'>
        <div class='clearfix'>
          <div class='float-left'>
            <!-- Row is the slow scope therefore is the way to access to the information that we are drawing -->
            {{row.item.description}}
          </div>
          <div class='float-right'>
             <!-- we use @click.stop here to prevent emitting of a 'row-clicked' event  -->
             <!-- https://bootstrap-vue.js.org/docs/components/table/#row-details-support -->
            <b-btn class='badge badge-pill badge-dark'  @click.stop='row.toggleDetails' v-model='row.detailsShowing'>
              {{row.detailsShowing ? $t('message.hide') : $t('message.why_question')}}
            </b-btn>
          </div>
        </div>
      </template>
      <!-- Likert scale -->
      <template slot='responseFormat' slot-scope='data'>
      <div>
         <!-- other whay to use the radio button group
           <b-form-group :id=''likertScale'+data.item.itemId'>
          <b-form-radio-group v-model='selected' :options='options' name='likertScale'>
          </b-form-radio-group>
        </b-form-group> -->
        <!-- <small>Not at all</small> -->
        <b-form-radio-group   :name="'likertScale'+data.item.itemId" :id="'likertScale'+data.item.itemId" v-model="responsesList[data.item.itemId].numericAnswer" >
          <!--<div v-for='(value, key) in appliesLikertOptions' :key='key' class='custom-control-inline col-md-1 py-0'>
            <div class="w-15" style="height=100%">
              <div class="h-70">
                <small>{{value.value === 5 || value.value === -1 || value.value === -2  || value.value === 0 ? value.text: '--------------'}}</small>
              </div>
              <div class="h-30">
                <b-form-radio :value='value.value' :id='data.item.itemId+value.value'/>
              </div>
            </div>
          </div> -->
          <div v-for='(value, key) in appliesLikertOptions' :key='key' class='custom-control-inline col-sm-4 py-0'>
            <b-form-radio :value='value.value' :id='data.item.itemId+value.value' >
              <small>{{value.text}}</small>
            </b-form-radio>
            <!-- <b-tooltip  :target='data.item.itemId+value.value' placement='top' v-show="value.value=-1">
                Hello <strong>World!</strong>
            </b-tooltip> -->
          </div>
        </b-form-radio-group>
      </div>
      </template>
      <!-- Checkbox needReview -->
      <template slot='needReview' slot-scope='row'>
         <div class='clearfix'>
          <div class='float-left'>
            <b-form-checkbox @click.native.stop @change='toggleReview(responsesList[row.item.itemId])' v-model='responsesList[row.item.itemId].needReview'/>
                <!-- se quita para no introducir ruido en la tabla
                {{$t('message.needs_review')}} -->
            <div class='form-group' v-show='responsesList[row.item.itemId].needReview'>
              <b-form-select v-model='responsesList[row.item.itemId].typeReview' :options='improvementOptions' class='mb-1'/>
              <b-form-group>
                  <small>{{$t('message.please_explain')}}</small>
                  <b-form-textarea id='commentsText' :rows='2' v-model='responsesList[row.item.itemId].reviewComments'></b-form-textarea>
                </b-form-group>
            </div>
          </div>
         </div>
      </template>
      <!-- Details of the review that the item migth need.  This slot should be named as row-details to work properly-->
      <template slot='row-details' slot-scope='row'>
        <item-justification :justification='row.item.justification' :itemName='row.item.name'></item-justification>
      </template>
      <template slot='N/A' slot-scope='row'>
         <b-form-checkbox v-model='responsesList[row.item.itemId].N_A' :id='"na"+row.item.itemId'/>
          <b-tooltip :trigger='hover' :target='"na"+row.item.itemId' placement='top'>
             {{$t('message.n_a_explanation')}}
          </b-tooltip>
      </template>

    </b-table>
    <nav>
      <b-pagination :total-rows='getRowCount(items)' :per-page='perPage' v-model='currentPage' prev-text='Prev' next-text='Next' hide-goto-end-buttons/>
    </nav>
  </b-card>
</template>

<script>

import BDData from './_BDData.js'
import itemJustification from './ItemJustification.vue'

export default {
  name: 'subItems-table',
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
    },
    improvementOptions: {
      type: Array,
      default: function () { return BDData.improvementOptions }
    },
    appliesLikertOptions: {
      type: Array,
      default: function () { return BDData.appliesLikertOptions }
    },
    hierarchicalLevel_two: {
      type: Object
    }
  },
  data: () => {
    return {
      responsesList: [],
      // items: BDData.customizedInstrument.itemsHierarchy.motivationHierarchy.hierarchicalItem.subHierarchicalItems.subItems
      subHierarchicalItems: [
        {
          'itemId': 'cri1',
          'suggestedImportance': {
            'numericValue': 1,
            'label': 'desirable'
          },
          'responseFormat': '',
          'name': 'Operational level 2',
          'description': 'Criterion 2',
          'justification': 'Criterion justification',
          'hierarchicalLevel': 2,
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
          }],
          subItems: [ {
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
            }],
            'N_A': false
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
            }],
            'N_A': false
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
            }
            ],
            'N_A': false
          }
          ]
        }],
      fields: [
        {key: 'itemId', label: 'Id', sortable: true},
        {key: 'name', sortable: true},
        {key: 'description'},
        {key: 'needReview', label: 'Needs review?'},
        {key: 'responseFormat', label: 'Response'},
        {key: 'N/A', label: 'N/A?'}
      ],
      currentPage: 1,
      perPage: 5,
      totalRows: 0
    }
  },
  created () {
    /* Creates a list of objects that will save user answers */
    var testResponseList = []
    for (var i = 0; i < this.items.length; i++) {
      var itemResponse = {}
      var itemId = this.items[i].itemId
      itemResponse.itemId = itemId
      itemResponse.needReview = false
      itemResponse.numericAnswer = null
      itemResponse.typeResponse = null
      itemResponse.reviewComments = null
      itemResponse.typeReview = null
      itemResponse.idResponseFormat = null
      testResponseList.push(itemResponse)
    }
    this.responsesList = {
      'subc1': testResponseList[0],
      'subc2': testResponseList[1],
      'subc3': testResponseList[2]
    }
    console.log('propertyComputed will update, as this.property is now reactive.')
  },
  methods: {
    getRowCount (items) {
      return items.length
    },
    toggleJustification (item) {
      /* Chages the justification of an item from visible to visible and vice-versa */
      item.showJustification = !item.showJustification
    },
    toggleReview (row) {
      /* Chages the justification of an item from visible to visible and vice-versa */
      row.needReview = !row.needReview
    },
    getLikertScaleLabel (value) {
      if (value === 1) {
        return 'N/A'
      } else {
        return '----'
      }
    }

  },
  components: {
    /* tag, component name */
    'item-justification': itemJustification
  }
}
</script>
