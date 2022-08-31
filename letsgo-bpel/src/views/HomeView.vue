<template>
  <div>
    <h1>BPEL Generation</h1>
      <v-btn @click="Addd('sequence') ,AddNewAct('Activity sequence created','test')">Sequence</v-btn>

      <v-btn @click="Addd('flow'), AddNewAct('Activity flow created','test')">Flow</v-btn>

      <v-btn @click="Addd('get'), AddNewAct('Activity Get created','test')">Get</v-btn>
      <v-btn @click="Addd('put'), AddNewAct('Activity Put created','test')">Put</v-btn>
      <v-btn @click="Addd('post'), AddNewAct('Activity Post created','test')">Post</v-btn>
      <v-btn @click="Addd('delete'), AddNewAct('Activity Delete created','test')">Delete</v-btn>  

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
          <v-btn color="primary" @click="dialog2 = false, Addd('if'), AddNewAct('Activity if created','test')" >Submit</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-btn @click="FileCreation()">Creer le fichier BPEL</v-btn>
      <v-list two-line >
         <v-list-item v-for="(Act,index) in ActList" :key="Act.id">
           <template v-slot:default="{ active }">
            <v-list-item-action>
              <v-checkbox :input-value="active"></v-checkbox>
            </v-list-item-action>
            <v-list-item-content>
               <v-list-item-title>{{Act.id}} - {{Act.title}} : {{Act.name}}</v-list-item-title>
               <v-btn text @click="deleteFromList(index)">
                <v-icon color="red"> mdi-delete-circle-outline </v-icon>
              </v-btn>
            </v-list-item-content>
              </template>
         </v-list-item>
      </v-list>

    
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
          ActList: [{id: 1,title: 'Activity process created',name:'fabrication',Activity: 'process'}],
          newText: '',
          nextId: 2,
          arr: [{nb: 1, Activity: 'process'}],
          nextNb: 2
        }
      },
      methods: {
      //Create array of activities---------
      Addd: function(Act){
        this.arr.push({
          nb: this.nextNb++,
          Activity: Act,
        })

      },
      //Add to hierarchy------------------
      AddNewAct: function(text, n){
         this.ActList.push({
        id: this.nextId++,
        title: text,
        name: n,
      })
      this.newText = '';
      },
     //Delete from hierarchy-------------
      deleteFromList: function(index) {
      this.ActList.splice(index, 1);
    },
        /*var FileSaver = require('file-saver');
        var blob = new Blob([xmlString], {type: "text/xml"});
        FileSaver.saveAs(blob, "test.xml");*/
        
    //Creation du fichier ------------------------------------------------
        FileCreation: function(){
          var serializer = new XMLSerializer();
          var doc = document.implementation.createDocument("", "", null);
          const pi = doc.createProcessingInstruction('xml', 'version="1.0" encoding="UTF-8"');
          doc.insertBefore(pi, doc.firstChild);
          var root = doc.createElement('process');
          for(var i=1; i < this.arr.length; i++){
                        console.log(this.arr[i].Activity);
          }
          
          for(var i=0; i < this.arr.length; i++){

            if(this.arr[i].Activity == 'sequence'){
              var activite = this.GenStr('sequence','test');
              root.appendChild(activite);
              const index = this.arr.indexOf('sequence');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
            };
            if(this.arr[i].Activity == 'flow'){
              var activite = this.GenStr('flow','test');
              root.appendChild(activite);
              const index = this.arr.indexOf('flow');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
              //activite.nextSibling;
              
            };
            if(this.arr[i].Activity == 'get'){
              var subactivite = this.GenCom('get','test');
              root.appendChild(subactivite);
              const index = this.arr.indexOf('get');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
              
            };
             if(this.arr[i].Activity == 'put'){
              var subactivite = this.GenCom('put','test');
              root.appendChild(subactivite);
              const index = this.arr.indexOf('put');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
              
            };
             if(this.arr[i].Activity == 'post'){
              var subactivite = this.GenCom('post','test');
              root.appendChild(subactivite);
              const index = this.arr.indexOf('post');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
              
            };
             if(this.arr[i].Activity == 'delete'){
              var subactivite = this.GenCom('delete','test');
              root.appendChild(subactivite);
              const index = this.arr.indexOf('delete');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
            };
             if(this.arr[i].Activity == 'if'){
              var subactivite = this.GenIf('if','test');
              root.appendChild(subactivite);
              const index = this.arr.indexOf('if');
               if (index > -1) { 
                this.arr.splice(index, 1); 
                }
              
            };
            //this.arr.splice(i,1);
          }
          this.arr[i] = [{nb: 1, Activity: 'process'}];
          doc.appendChild(root);
          //Afficher le document dans la console------------
          var xmlString = serializer.serializeToString(doc);
          console.log(xmlString);
        },
         //SEQUENCE/FLOW----------------------------------------------------
        GenStr: function(ActivityType,name){
          var doc = document.implementation.createDocument("", "", null);
          switch(ActivityType) {
            case 'sequence':
              // create sequence
            var tmp = doc.createElement("sequence");
            tmp.setAttribute("name", name);
            tmp.setAttribute("DD", "");
            tmp.setAttribute("DF", "");
            tmp.setAttribute("DUR", "");
            tmp.setAttribute("CD", "");
            tmp.setAttribute("CF", "");
            tmp.setAttribute("TC", "");
            tmp.setAttribute("InstantDebut", "");
            break;
            case 'flow':
              // create flow
            var tmp = doc.createElement("flow");
            tmp.setAttribute("name", name);
            tmp.setAttribute("DD", "");
            tmp.setAttribute("DF", "");
            tmp.setAttribute("DUR", "");
            tmp.setAttribute("CD", "");
            tmp.setAttribute("CF", "");
            tmp.setAttribute("TC", "");
            tmp.setAttribute("InstantDebut", "");
            break;
          }
            return tmp;
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
            var tmp = doc.createElement("get");
            tmp.setAttribute("name", name);
            tmp.setAttribute("uri", "");
            tmp.setAttribute("DD", "");
            tmp.setAttribute("DF", "");
            tmp.setAttribute("DUR", "");
            tmp.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(tmp);
            break;
            case 'put':
              // create put
            var tmp = doc.createElement("put");
            tmp.setAttribute("name", name);
            tmp.setAttribute("uri", "");
            tmp.setAttribute("DD", "");
            tmp.setAttribute("DF", "");
            tmp.setAttribute("DUR", "");
            tmp.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(tmp);
            break;
            case 'post':
              // create post
            var tmp = doc.createElement("post");
            tmp.setAttribute("name", name);
            tmp.setAttribute("uri", "");
            tmp.setAttribute("DD", "");
            tmp.setAttribute("DF", "");
            tmp.setAttribute("DUR", "");
            tmp.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(tmp);
            break;
            case 'delete':
              // create delete
            var tmp = doc.createElement("delete");
            tmp.setAttribute("name", name);
            tmp.setAttribute("uri", "");
            tmp.setAttribute("DD", "");
            tmp.setAttribute("DF", "");
            tmp.setAttribute("DUR", "");
            tmp.setAttribute("InstantDebut", "");
            extensionActivity.appendChild(tmp);
            break;
          }
        return extensionActivity;
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
          //process.appendChild(iff);
          //doc.appendChild(process);
          //var xmlString = serializer.serializeToString(doc);
          //console.log(xmlString);
          return iff;
        }
      }
      //BOUCLE WHILE -------------------------------
  }
</script>