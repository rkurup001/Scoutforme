<template>
  <Page class="page" loaded="onPageLoaded">
    <ActionBar title="Home" class="action-bar">
      <ActionItem
        tap="onEdit"
        ios.systemIcon="2"
        android.systemIcon="ic_menu_edit"
        ios.position="right"
      />
    </ActionBar>

    <ScrollView>
      <StackLayout class="home-panel">
          <Button text="New Pit Form" @tap="onPitFormTap"/>
        <Button text="New Match Form" @tap="onMatchFormTap"/>
        <!--Add your page content here-->
        <ListView
          class="list-group"
          for="scoutform in scoutforms"
          @itemTap="onItemTap"
          style="height:1050px"
        >
          <v-template>
            <FlexboxLayout flexDirection="row" class="list-group-item">
              <Label :text="scoutform.name" class="list-group-item-heading" style="width: 60%"/>
            </FlexboxLayout>
          </v-template>
        </ListView>
         </StackLayout>
    </ScrollView>
  </Page>
</template>

<script>
import { knownFolders, Folder, File, path } from "tns-core-modules/file-system";
import PitScoutForm from "./PitScoutForm";
import MatchScoutForm from "./MatchScoutForm";


const ObservableArray = require("tns-core-modules/data/observable-array").ObservableArray;
// Change the items source from Array to the NativeScript's ObservableArray

export default {
  methods: {
    getFormOnDisk() {
      let scoutfiles = new ObservableArray([]);
      
      //let documents = knownFolders.documents();
      let externaldocpath = path.join(
        android.os.Environment.getExternalStoragePublicDirectory(
          android.os.Environment.DIRECTORY_DCIM
        ).toString()
      );
      let documents = Folder.fromPath(externaldocpath);
      console.log(externaldocpath);
      console.log(Folder.exists(externaldocpath));
      console.log(documents);

      documents
        .getEntities()
        .then(entities => {
          // entities is array with the document's files and folders.
          entities.forEach(entity => {
            if (entity.name.startsWith("pitscouting") || entity.name.startsWith("match-")) {
            scoutfiles.push({
              name: entity.name,
              path: entity.path,
              lastModified: entity.lastModified.toString()
            });
            }
          });
        })
        .catch(err => {
          // Failed to obtain folder's contents.
          console.log(err.stack);
        });
      return scoutfiles;
    },
    onPitFormTap() {
      this.$navigateTo(PitScoutForm, {  props: { scoutinglist: this.scoutforms }});
    },
    onMatchFormTap() {
      this.$navigateTo(MatchScoutForm, { props: { scoutinglist: this.scoutforms }});
    },

    onItemTap: function(args) {
        //scoutingfiles.push({name: "The Da Vinci Code zzzz" });
      //console.log("Item with index: " + args.index + " tapped");
    }
  },

  data() {
    return {
      scoutforms: this.getFormOnDisk(),
      selectedListPickerIndex: 0
    };
  }
};
</script>

<style scoped>
.home-panel {
  vertical-align: center;
  font-size: 20;
  margin: 15;
}

.description-label {
  margin-bottom: 15;
}
</style>