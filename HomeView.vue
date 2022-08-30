<template>
  <div>
    <h1>BPEL Generation</h1>

      <v-btn @click="GenStr('process','sequence','phase1'), AddNewAct('Activity sequence created','phase1')">Sequence</v-btn>
      <v-btn @click="GenStr('process','flow','phase1'), AddNewAct('Activity flow created','phase2')">Flow</v-btn>
      <v-btn id="g">Get</v-btn>
      <v-btn id="p">Put</v-btn>
      <v-btn id="po">Post</v-btn>
      <v-btn id="d">Delete</v-btn>  
      <v-list two-line >
         <v-list-item v-for="(Act,index) in ActList" :key="Act.id">
            <v-list-item-content>
               <v-list-item-title>{{Act.id}} - {{Act.title}} : {{Act.name}}</v-list-item-title>
               <v-btn text @click="deleteFromList(index)">
                <v-icon color="red"> mdi-delete-circle-outline </v-icon>
              </v-btn>
            </v-list-item-content>
         </v-list-item>
      </v-list>
  
    <v-btn @click="dialog2 = !dialog2">If</v-btn>
    
    <v-dialog v-model="dialog2" max-width="500px">
      <v-card>
        <v-card-text>
        <h1 class="pa-4">Condition</h1>
        <v-text-field v-model="conditionIF" color="#673AB7" label="Condition" placeholder="Type a condition here ... " class="mt-8" outlined dense></v-text-field>
        <!-- <span>value: {{ conditionIF }}</span> -->
        <span class="red--text">* Could be a boolean or an expression</span>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <!-- ajouter une regex pour la condition -->
        <v-btn color="primary" @click="dialog2 = false, GenIf('if',conditionIF)" >Submit</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
  export default {
      data(){
        return{
          dialog2: false,
          conditionIF: '',
          name: 'test',
          ParentActivity: '',
          ActivityType: ['sequence','flow','get','put','post','delete','if','ifelse'],
          ActList: [{id: 1,title: 'Activity process created',name:'fabrication'}],
          newText: '',
          nextId: 2
        }
      },
      methods: {
      //Add to hierarchy------------------
      AddNewAct: function(text, n){
         this.ActList.push({
        id: this.nextId++,
        title: text,
        name: n
      })
      this.newText = '';
      },
     //Delete from hierarchy-------------
      deleteFromList: function(index) {
      this.ActList.splice(index, 1);
    },
      //Creation document
      DocCreate: function(document,Parent){
          var serializer = new XMLSerializer();
          document.appendChild(Parent);
          var xmlString = serializer.serializeToString(document);
          console.log(xmlString);
      },
      //SEQUENCE/FLOW----------------------------------------------------
        GenStr: function(ParentActivity,ActivityType,name){
          
          var doc = document.implementation.createDocument("", "", null);
          var Parent= doc.createElement(ParentActivity);
          
          const pi = doc.createProcessingInstruction('xml', 'version="1.0" encoding="UTF-8"');
          doc.insertBefore(pi, doc.firstChild);
          
          switch(ActivityType) {
            case 'sequence':
              // create sequence
            var sequence = doc.createElement("sequence");
            sequence.setAttribute("name", name);
            sequence.setAttribute("DD", "");
            sequence.setAttribute("DF", "");
            sequence.setAttribute("DUR", "");
            sequence.setAttribute("CD", "");
            sequence.setAttribute("CF", "");
            sequence.setAttribute("TC", "");
            sequence.setAttribute("InstantDebut", "");
            Parent.appendChild(sequence);
            break;
            case 'flow':
              // create flow
            var flow = doc.createElement("flow");
            flow.setAttribute("name", name);
            flow.setAttribute("DD", "");
            flow.setAttribute("DF", "");
            flow.setAttribute("DUR", "");
            flow.setAttribute("CD", "");
            flow.setAttribute("CF", "");
            flow.setAttribute("TC", "");
            flow.setAttribute("InstantDebut", "");
            Parent.appendChild(flow);
            break;
          }
          this.DocCreate(doc,Parent);
        /*var FileSaver = require('file-saver');
        var blob = new Blob([xmlString], {type: "text/xml"});
        FileSaver.saveAs(blob, "test.xml");*/
        },
        /*BPEL FILE CREATION*/
        CreateScenario() {
          console.log('?');
          // 2 possibilities -------------------------
          // 1 - imbrique ----------------------------
          // 2 - non imbrique ------------------------
        },
        //GET,POST,PUT,DELETE-------------------------------------------------
        GenCom: function(ActivityType, name){
          var serializer = new XMLSerializer();
          var doc = document.implementation.createDocument("", "", null);
          var extensionActivity = doc.createElement("extensionActivity");
          
          const pi = doc.createProcessingInstruction('xml', 'version="1.0" encoding="UTF-8"');
          doc.insertBefore(pi, doc.firstChild);
          
          switch(ActivityType) {
            case 'get':
              // create get
            var get = doc.createElement("get");
            get.setAttribute("name", name);
            get.setAttribute("uri", "");
            get.setAttribute("DD", "");
            get.setAttribute("DF", "");
            get.setAttribute("DUR", "");
            get.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(get);
            break;
            case 'put':
              // create put
            var put = doc.createElement("put");
            put.setAttribute("name", name);
            put.setAttribute("uri", "");
            put.setAttribute("DD", "");
            put.setAttribute("DF", "");
            put.setAttribute("DUR", "");
            put.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(put);
            break;
            case 'post':
              // create post
            var post = doc.createElement("post");
            post.setAttribute("name", name);
            post.setAttribute("uri", "");
            post.setAttribute("DD", "");
            post.setAttribute("DF", "");
            post.setAttribute("DUR", "");
            post.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(post);
            break;
            case 'delete':
              // create delete
            var deletee = doc.createElement("delete");
            deletee.setAttribute("name", name);
            deletee.setAttribute("uri", "");
            deletee.setAttribute("DD", "");
            deletee.setAttribute("DF", "");
            deletee.setAttribute("DUR", "");
            deletee.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(deletee);
            break;
          }
        doc.appendChild(extensionActivity);
        var xmlString = serializer.serializeToString(doc);
        console.log(xmlString);
        },

        //IF ELSE-------------------------------------------------
        GenIf: function(ActivityType, conditionIF){
          var serializer = new XMLSerializer();
          var doc = document.implementation.createDocument("", "", null);
          // Process sera remplace par ParentActivity
          var process = doc.createElement("process");
      
          const pi = doc.createProcessingInstruction('xml', 'version="1.0" encoding="UTF-8"');
          doc.insertBefore(pi, doc.firstChild);
          var iff = doc.createElement('if');
          var ifcondition = doc.createElement('condition');
          var conditionif = doc.createTextNode(conditionIF);
          ifcondition.appendChild(conditionif);
          iff.appendChild(ifcondition);
          if(ActivityType == 'ifelse'){
            var elsee = doc.createElement('else');
            iff.appendChild(elsee);
          }
          process.appendChild(iff);
          doc.appendChild(process);
          var xmlString = serializer.serializeToString(doc);
          console.log(xmlString);
        }
      }
  }
</script>