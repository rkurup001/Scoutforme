
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
var dialogs = require("tns-core-modules/ui/dialogs");
//import { PitScoutViewModel } from "./../view-models/pitscoutmodel";
import RadDataForm from "nativescript-ui-dataform/vue";
var utilsModule = require("tns-core-modules/utils/utils");
Vue.use(RadDataForm);
var Observable = require("data/observable").Observable;
var scouting = new Observable();
var pageData = new Observable();

export default {
  props: ["scoutinglist"],
  name: "MatchScoutForm",
  data() {
    return {
      scouting: {
        ScoutName: "",
        MatchNumber: 0,
        teamNumber: "",
        startLevel: "",
        robotfocus: "",
        leaveHab: "",
        hatchattempted: 0,
        hatchplaced: 0,
        cargoattempted: 0,
        cargoplaced: 0,
        defense: "",
        teleopcargohatch: "",
        loadingstationground: "",
        cargoshipHatchattempted: 0,
        cargoshipHatchplaced: 0,
        cargoshipCargoattempted: 0,
        cargoshipCargoplaced: 0,
        rocketlevel: "",
        rocketHatchattempted: 0,
        rocketHatchplaced: 0,
        rocketCargoattempted: 0,
        rocketCargoplaced: 0,
        climb: "",
        climbFinishLevel: "",
        climbClimbAuto: "",
        climbAssistOther: "",
        penalties: "",
        comments: ""
      },
      committedScouting: "",
      radmetadata: {
        isReadOnly: false,
        commitMode: "Immediate",
        validationMode: "Immediate",
        propertyAnnotations: [
          {
            name: "ScoutName",
            displayName: "Your Name",
            index: 0,
            required: true,
            editor: "Text",
            validators: [{ name: "NonEmpty" }]
          },
          {
            name: "MatchNumber",
            displayName: "Match Number",
            index: 1,
            required: true,
            editor: "Number",
            validators: [{ name: "NonEmpty" }]
          },
          {
            name: "teamNumber",
            displayName: "Team Being Scouted",
            index: 2,
            editor: "Number",
            required: true,
            validators: [{ name: "NonEmpty" }]
          },
          {
            name: "startLevel",
            displayName:
              "Does the robot start on the lower level (level 1) or the higher level (level 2)? *",
            index: 3,
            required: true,
            editor: "Picker",
            valuesProvider: ["Level 1", "Level 2"]
          },
          {
            name: "robotfocus",
            displayName:
              "Does the robot focus on hatches, cargo, neither, or both? *",
            index: 4,
            required: true,
            editor: "Picker",
            valuesProvider: ["Hatch", "Cargo", "Neither", "Both"]
          },
          {
            name: "leaveHab",
            displayName: "Can it get out of the HAB? *",
            index: 5,
            required: true,
            editor: "Picker",
            valuesProvider: ["Yes", "No"]
          },
          {
            name: "hatchattempted",
            displayName: "How many hatches does it attempt to place? *",
            index: 6,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
            // editor: "Number",

            // validators: [{
            //   name: 'RangeValidator',
            //     'params': {
            //       'minimum': 1,
            //       'maximum': 30,
            //       'errorMessage': 'Values must be between 1-30.',
            //     }
            // }]
          },
          {
            name: "hatchplaced",
            displayName: "How many hatches does it place? *",
            index: 7,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "cargoattempted",
            displayName: "How many cargoes does it attempt to place? *",
            index: 8,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "cargoplaced",
            displayName: "How many cargoes does it place? *",
            index: 9,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "defense",
            displayName: "Does the robot play defense? *",
            index: 10,
            required: true,
            editor: "Picker",
            valuesProvider: ["Yes", "No"]
          },
          {
            name: "teleopcargohatch",
            displayName:
              "During teleop does team collect cargo or hatches, or both? *",
            index: 11,
            required: true,
            editor: "Picker",

            valuesProvider: ["Neither", "Cargo", "Hatches", "Both"]
          },
          {
            name: "loadingstationground",
            displayName:
              "Do they collect from the loading station or ground or both? *",
            index: 12,
            required: true,
            editor: "Picker",
            valuesProvider: ["Neither", "Loading Station", "Ground", "Both"]
          },
          {
            name: "cargoshipHatchattempted",
            displayName:
              "Cargo Ship: How many hatches did it attempt to place? *",
            index: 13,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "cargoshipHatchplaced",
            displayName: "Cargo Ship: How many hatches did it place? *",
            index: 14,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "cargoshipCargoattempted",
            displayName:
              "Cargo Ship: How many cargoes did it attempt to place? *",
            index: 15,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "cargoshipCargoplaced",
            displayName: "Cargo Ship: How many cargoes did it place? *",
            index: 16,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "rocketlevel",
            displayName:
              "Which levels of the rocket can they put game pieces on? (Check all that apply) *",
            index: 17,
            required: true,
            editor: "Picker",
            valuesProvider: ["None", "High", "Middle", "Low"]
          },
          {
            name: "rocketHatchattempted",
            displayName: "Rocket: How many hatches did it attempt to place? *",
            index: 18,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "rocketHatchplaced",
            displayName: "Rocket: How many hatches did it place? *",
            index: 19,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "rocketCargoattempted",
            displayName: "Rocket: How many cargoes did it attempt to place? *",
            index: 20,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "rocketCargoplaced",
            displayName: "Rocket: How many cargoes did it place? *",
            index: 21,
            required: true,
            editor: "Stepper",
            editorParams: {
              minimum: 0,
              maximum: 30
            }
          },
          {
            name: "climb",
            displayName: "Did the robot climb?",
            index: 22,
            required: true,
            editor: "Picker",
            valuesProvider: ["Yes", "No", "Attempted to climb"]
          },
          {
            name: "climbFinishLevel",
            displayName:
              "If it did climb, what level of the HAB did it finish on? *",
            index: 23,
            required: true,
            editor: "Picker",
            valuesProvider: ["Level 1", "Level 2", "Level 3"]
          },
          {
            name: "climbClimbAuto",
            displayName: "Did the Robot Climb by themselves? *",
            index: 24,
            required: true,
            editor: "Picker",
            valuesProvider: ["Yes", "No", "Didn't climb"]
          },
          {
            name: "climbAssistOther",
            displayName:
              "During the end phase, does the team help other teams climb? *",
            index: 25,
            required: true,
            editor: "Picker",
            valuesProvider: ["Yes", "No"]
          },
          {
            name: "penalties",
            displayName: "Any penalties? *",
            index: 26,
            required: true,
            editor: "Picker",
            valuesProvider: [ "None","Yellow Card", "Red Card"]
          },
          {
            name: "comments",
            displayName: "Comments?",
            index: 76,
            required: true,
            editor: "Text"
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

      fname =
        "match-" + data.MatchNumber + "scouting-" + data.teamNumber + ".json";

      // Get file name
      console.log(fname);
      console.log(this.committedScouting[0]);

      let myfile = documents.getFile(fname);
      myfile.writeTextSync(this.committedScouting, function() {
        dialogs.confirm({ title: "Error", message: "Unable to save!!!" });
      });
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