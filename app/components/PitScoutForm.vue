
<template>
  <Page @loaded="onPageLoaded">
    <ActionBar title="Home" class="action-bar">
      <ActionItem
        @tap="onSave()"
        ios.systemIcon="16"
        ios.position="right"
        text="Save"
        android.position="popup"
      ></ActionItem>
    </ActionBar>
    <RadDataForm
      id="myDataForm"
      :source="scouting"
      :metadata="radmetadata"
      @propertyCommitted="onPropertyCommitted"
    ></RadDataForm>
  </Page>
</template>

<script>
import Vue from "nativescript-vue";
import { knownFolders, Folder, File, path } from "tns-core-modules/file-system";
//import { PitScoutViewModel } from "./../view-models/pitscoutmodel";
var dialogs = require("tns-core-modules/ui/dialogs");
import RadDataForm from "nativescript-ui-dataform/vue";
var utilsModule = require("tns-core-modules/utils/utils");
Vue.use(RadDataForm);
var Observable = require("data/observable").Observable;
var scouting = new Observable();
var pageData = new Observable();

export default {
   props: ['scoutinglist'],
  name: "PitScoutForm",
  data() {
    return {
      scouting: {
        teamNumber: "",
        robotfocus: "",
        weakness: "",
        strength: "",
        drivetrain: "",
        hatchcollected: "",
        cargopickup: "",
        climbmethod: "",
        sandstorm: "",
        autoCapability: "",
        yearofexpDriver: 0,
        yearofexpCoach: 0,
        yearofexpOperator: 0,
        yearofexpHumanPlayer: 0
      },
      committedScouting: "",
      radmetadata: {
        isReadOnly: false,
        commitMode: "Immediate",
        validationMode: "Immediate",
        propertyAnnotations: [
          {
            name: "teamNumber",
            displayName: "Team Number",
            index: 0,
            editor: "Number"
          },
          {
            name: "robotfocus",
            displayName: "What is the main focus of your robot?",
            index: 1,
            editor: "Text"
          },
          {
            name: "weakness",
            displayName: "What are the weaknesses of you robot?",
            index: 2,
            editor: "Text"
          },
          {
            name: "strength",
            displayName: "What are the strengths of your robot?",
            index: 3,
            editor: "Text"
          },
          {
            name: "drivetrain",
            displayName: "What is your drive train?",
            index: 4
          },
          {
            name: "hatchcollected",
            displayName: "How do you acquire hatches? (if they do)",
            index: 5
          },
          {
            name: "cargopickup",
            displayName: "How do you acquire cargo? (if they do)",
            index: 6
          },
          {
            name: "climbmethod",
            displayName: "How do you climb? (if they do)",
            index: 7
          },
          {
            name: "sandstorm",
            displayName:
              "During Sandstorm, is your robot controlled autonomously or manually?",
            index: 8
          },
          {
            name: "autoCapability",
            displayName:
              "What are your autonomous capabilities (if they drive auto in sandstorm)?",
            index: 9
          },
          {
            name: "yearofexpDriver",
            displayName: "How many years of experience does your driver have?",
            index: 10,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 10
            }
          },
          {
            name: "yearofexpCoach",
            displayName: "How many years of experience does your coach have?",
            index: 11,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 10
            }
          },
          {
            name: "yearofexpHumanPlayer",
            displayName:
              "How many years of experience does your human player have?",
            index: 12,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 10
            }
          },
          {
            name: "yearofexpOperator",
            displayName:
              "How many years of experience does your operator have?",
            index: 13,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 10
            }
          }
        ]
      }
    };
  },
  methods: {
    onSave: function() {
      utilsModule.ad.dismissSoftInput();
      let externaldocpath = path.join(
        android.os.Environment.getExternalStoragePublicDirectory(
          android.os.Environment.DIRECTORY_DCIM
        ).toString()
      );
      let documents = Folder.fromPath(externaldocpath);
      var fname;
      var data = JSON.parse(this.committedScouting);
      
      fname = "pitscouting-" + data.teamNumber + ".json";
      let myfile = documents.getFile(fname);
      myfile.writeTextSync(this.committedScouting);
      dialogs.alert("Data saved!!!");
      this.scoutinglist.push({
         name: myfile.name,
              path: myfile.path,
              lastModified: myfile.lastModified.toString()

      });
      
      this.$navigateBack();
    },
    onPropertyCommitted(data) {
      this.committedScouting = data.object.editedObject;

    },
    onPageLoaded(args) {}
  }
};
</script>

<style>
RadDataForm {
  background-color: rgb(216, 230, 217);
  color: #3f51b5;
  padding: 5;
  margin: 5;
  border-color: #303f9f;
  border-width: 5;
  border-radius: 5;
}

PropertyEditor {
  background-color: transparent;
  separator-color: #303f9f;
}
</style>