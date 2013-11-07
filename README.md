/*
 * TERMS OF REPRODUCTION USE
 *
 * Failure to follow these terms will result in me getting very angry at you
 * and having your software tweaked or removed if possible. Either way, you're
 * still an idiot for not following such a basic rule.
 * NO EVENT SHALL THE AUTHORS
 * BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER
 * RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR
 * OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
 *
 */
 
/*
 * NOTE:  PLEASE CONTACT DJ-NEON FOR THIS SCRIPT (DO NOT CHANGE ANYTHING ON THIS SCRIPT OR USE THIS SCRIPT WHICH WAS WRITTEN BY
 * IT'S RIGHTFUL OWNER: DJ NOEN)
 *
 * @Author         Tawi Jordan - ๖ۣۜĐJ - ɴᴇᴏɴ - TFL (Member. on Plug.dj)
 *
 */
 
 
var mubBot = {};
mubBot.misc = {};
mubBot.settings = {};
mubBot.moderators = {};
mubBot.filters = {};
botMethods = {};
mubBot.pubVars = {};
 
toSave = {};
toSave.settings = mubBot.settings;
toSave.moderators = mubBot.moderators;
 
mubBot.misc.version = "2.0.25";
mubBot.misc.origin = "This bot was created by Redstoner and DJ-Neon alone, and it is copyrighted!";
mubBot.misc.changelog = "Added a secondary check for history";
mubBot.misc.ready = true;
mubBot.misc.lockSkipping = false;
mubBot.misc.lockSkipped = "0";
mubBot.misc.tacos = new Array();
 
joined = new Date().getTime();
 
cancel = false;
 
mubBot.filters.swearWords = new Array();
mubBot.filters.racistWords = new Array();
mubBot.filters.beggerWords = new Array();
 
mubBot.settings.maxLength = 5; //minutes
mubBot.settings.cooldown = 10; //seconds
mubBot.settings.staffMeansAccess = true;
mubBot.settings.historyFilter = true;
mubBot.settings.swearFilter = true;
mubBot.settings.racismFilter = true;
mubBot.settings.beggerFilter = true;
mubBot.settings.interactive = true;
mubBot.settings.removedFilter = true;
 
//Redstoner              [DJ-NEON]                  [Redstoner-bot]
mubBot.admins = ["50aeaeb6c3b97a2cb4c25bd2", "524b568f3e083e4f2be7356e"];
 
mubBot.filters.swearWords = ["slut","mofo","penis","penus","fuck","shit","bitch","cunt","twat","faggot","queer","dumbass","pussy","dick","cocksucker","asshole","vagina","tit","mangina","tits","cock","jerk","puta","puto","cum","sperm"];
 
mubBot.filters.racistWords = ["nigger","kike","spick","porchmonkey","camel jockey","towelhead","towel head","chink","gook","porch monkey", "nigga"];
 
mubBot.filters.beggerWords = ["fan4fan","fan me","fan pls","fans please","fan please","fan 4 fan","fan back","give me fans","gimme fans","fan back", "how do i","fan for fan", "fanforfan", "F-", "F@N", "Fan i fan", "fanifan", "fan1fan"];
 
mubBot.misc.tacos = ["crispy taco","mexican taco","vegetarian taco","spicy taco","meatlover taco","cheese taco","wet hamburger","taco shell","delicious taco","gross taco"];
 
mubBot.pubVars.skipOnExceed;
mubBot.pubVars.command = false;
 
Array.prototype.remove=function(){var c,f=arguments,d=f.length,e;while(d&&this.length){c=f[--d];while((e=this.indexOf(c))!==-1){this.splice(e,1)}}return this};
 
API.on(API.DJ_ADVANCE, djAdvanceEvent);
 
function djAdvanceEvent(data){
    setTimeout(function(){ botMethods.djAdvanceEvent(data); }, 500);
}
 
botMethods.skip = function(){
    setTimeout(function(){
        if(!cancel) API.moderateForceSkip();
    }, 3500);
};
 
botMethods.load = function(){
    toSave = JSON.parse(localStorage.getItem("BotSave"));
    mubBot.settings = toSave.settings;
    ruleSkip = toSave.ruleSkip;
};
 
botMethods.save = function(){localStorage.setItem("BotSave", JSON.stringify(toSave))};
 
botMethods.loadStorage = function(){
    if(localStorage.getItem("BotSave") !== null){
        botMethods.load();
    }else{
        botMethods.save();
    }
};
 
botMethods.checkHistory = function(){
    currentlyPlaying = API.getMedia(), history = API.getHistory();
    caught = 0;
    for(var i = 0; i < history.length; i++){
        if(currentlyPlaying.cid === history[i].media.cid){
            caught++;
        }
    }
    caught--;
    return caught;
};
 
botMethods.getID = function(username){
    var users = API.getUsers();
    var result = "";
    for(var i = 0; i < users.length; i++){
        if(users[i].username === username){
            result = users[i].id;
            return result;
        }
    }
 
    return "notFound";
};
 
botMethods.cleanString = function(string){
    return string.replace(/&#39;/g, "'").replace(/&amp;/g, "&").replace(/&#34;/g, "\"").replace(/&#59;/g, ";").replace(/&lt;/g, "<").replace(/&gt;/g, ">");
};
 
botMethods.djAdvanceEvent = function(data){
    clearTimeout(mubBot.pubVars.skipOnExceed);
    if(mubBot.misc.lockSkipping){
        API.moderateAddDJ(mubBot.misc.lockSkipped);
        mubBot.misc.lockSkipped = "0";
        mubBot.misc.lockSkipping = false;
        setTimeout(function(){ API.moderateRoomProps(false, true); }, 500);
    }
    var song = API.getMedia();
    if(botMethods.checkHistory() > 0 && mubBot.settings.historyFilter){
        if(API.getUser().permission < 2){
            API.sendChat("This song is in the history! You should make me a mod so that I could skip it!");
        }else if(API.getUser().permission > 1){
            API.sendChat("@" + API.getDJs()[0].username + ", playing songs that are in the history isn't allowed, please check next time! Skipping..");
            botMethods.skip()
        }else if(song.duration > mubBot.settings.maxLength * 60){
            mubBot.pubVars.skipOnExceed = setTimeout( function(){
                API.sendChat("@"+API.getDJs()[0].username+" You have now played for as long as this room allows, time to let someone else have the booth!");
                botMethods.skip();
            }, mubBot.settings.maxLength * 60000);
            //API.sendChat("@"+API.getDJs()[0].username+" This song will be skipped " + mubBot.settings.maxLength + " minutes from now because it exceeds the max song length.");
        }else{
            setTimeout(function(){
                if(botMethods.checkHistory() > 0 && mubBot.settings.historyFilter){
                    API.sendChat("@" + API.getDJs()[0].username + ", playing songs that are in the history isn't allowed, please check next time! Skipping..");
                    botMethods.skip()
                };
            }, 1500);
        }
    }
};
 
    API.on(API.CHAT, function(data){
        if(data.message.indexOf('!') === 0){
            var msg = data.message, from = data.from, fromID = data.fromID;
            var command = msg.substring(1).split(' ');
            if(typeof command[2] != "undefined"){
                for(var i = 2; i<command.length; i++){
                    command[1] = command[1] + ' ' + command[i];
                }
            }
            if(mubBot.misc.ready || mubBot.admins.indexOf(fromID) > -1 || API.getUser(data.fromID).permission > 1){
                switch(command[0].toLowerCase()){
                    case "ping":
                        API.sendChat("@"+data.from+ " PONG!");
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
                    case "weird":
                    case "weirdday":
                    case "wierd":
                    case "wierdday":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("Weird Songs - http://playmc.pw/plug/WeirdDay.html");
                        }else if(command[1].indexOf("@") > -1){
                            API.sendChat(command[1]+" Weird Songs - http://playmc.pw/plug/WeirdDay.html");
                        }else{
                            API.sendChat("Weird Songs - http://playmc.pw/plug/WeirdDay.html");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
                       
                    case "usercommands":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("User Commands - http://goo.gl/GErBae");
                        }else if(command[1].indexOf("@") > -1){
                            API.sendChat(command[1]+" User Commands - http://goo.gl/GErBae");
                        }else{
                            API.sendChat("User Commands - http://goo.gl/GErBae");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
                       
                    case "modcommands":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("Mods Commands - http://goo.gl/P5bfbJ");
                        }else if(command[1].indexOf("@") > -1){
                            API.sendChat(command[1]+" Mods Commands - http://goo.gl/P5bfbJ");
                        }else{
                            API.sendChat("Mods Commands - http://goo.gl/P5bfbJ");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "users":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("User List - http://rscommands.blogspot.com/");
                        }else if(command[1].indexOf("@") > -1){
                            API.sendChat(command[1]+" User List - http://rscommands.blogspot.com/");
                        }else{
                            API.sendChat("User List - http://rscommands.blogspot.com/");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
                       
                    case "wiki":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("@"+data.from+" https://en.wikipedia.org/wiki/Special:Random");
                        }else{
                            var r = data.message.substring(6).replace(/ /g, "_");
                            $.getJSON("http://jsonp.appspot.com/?callback=?&url=" + escape("http://en.wikipedia.org/w/api.php?action=query&prop=links&format=json&titles="+r.replace(/ /g,"_")),
                                function(wikiData){
                                    if (!wikiData || !wikiData.query || !wikiData.query.pages) // there's an error. pssh, don't let anyone know ;)
                                        return API.sendChat("@"+data.from+" http://en.wikipedia.org/wiki/"+r+" (NOT GUARANTEED TO BE CORRECT)");
                                    if (wikiData.query.pages[-1]) {
                                        API.sendChat("@"+data.from+" article not found");
                                    }else{
                                        for (var i in wikiData.query.pages)
                                            // note: the #... is just to make the url look nicer
                                            return API.sendChat("@"+data.from+" https://en.wikipedia.org/wiki/?curid="+i+"#"+escape(wikiData.query.pages[i].title) );
                                    }
                                }
                            );
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "link":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("Our Web - http://redstoner.com/");
                        }else if(command[1].indexOf("@") > -1){
                            API.sendChat(command[1]+" Our Web - http://redstoner.com/");
                        }else{
                            API.sendChat("Our Web - http://redstoner.com/");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "twitter":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("Twitter - https://twitter.com/RedstonerServer");
                        }else if(command[1].indexOf("@") > -1){
                            API.sendChat(command[1]+" Twitter - https://twitter.com/RedstonerServer");
                        }else{
                            API.sendChat("Twitter - https://twitter.com/RedstonerServer");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "linkify":
                        if(typeof command[1] == "undefined"){
                            API.sendChat("@" + data.from + " You need to put a link!");
                        }else if(command[1].toLowerCase().indexOf("plug.dj") === -1 && command[1].toLowerCase().indexOf("bug.dj") === -1){
                            API.sendChat("http://"+command[1]);
                        }else{
                            API.sendChat("Nice try! Advertising is not allowed in this room.");
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "songlink":
                        if(API.getMedia().format == 1){
                            API.sendChat("@" + data.from + " " + "http://youtu.be/" + API.getMedia().cid);
                        }else{
                            var id = API.getMedia().cid;
                            SC.get('/tracks', { ids: id,}, function(tracks) {
                                API.sendChat("@"+data.from+" "+tracks[0].permalink_url);
                            });
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "source":
                        API.sendChat("@"+data.from+ " http://goo.gl/bsvbY8");
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                }
            }
        }
    });
 
    API.on(API.CHAT, function(data){
        if(data.message.indexOf('!') === 0){
            var msg = data.message, from = data.from, fromID = data.fromID;
            var command = msg.substring(1).split(" ");
            if(typeof command[2] != "undefined"){
                for(var i = 2; i<command.length; i++){
                    command[1] = command[1] + " " + command[i];
                }
            }
            if(mubBot.misc.ready || mubBot.admins.indexOf(fromID) > -1 || API.getUser(fromID).permission > 1){
                switch(command[0].toLowerCase()){
                    /* commented out because the bot isn't running on a dedicated bot account
                     case "meh":
                     if(API.getUser(data.fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) $("#button-vote-negative").click();
                     break;
 
                     case "woot":
                     if(API.getUser(data.fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1)  $("#button-vote-positive").click();
                     break;*/
 
                    case "say":
                    if(API.getUser(data.fromID).permission > 1){
                        if(typeof command[1] === "undefined"){
                        }else{
                            API.sendChat(" "+command[1]);
                        }
                    }
                        break;
 
                    case 'cancel':
                        cancel = true;
                        API.sendChat('AutoSkip cancelled');
                        break;
 
                    case "lockskip":
                        if( API.getUser(data.fromID).permission > 1){
                            API.moderateRoomProps(true, true);
                            mubBot.misc.lockSkipping = true;
                            mubBot.misc.lockSkipped = API.getDJs()[0].id;
                            setTimeout(function(){ API.moderateRemoveDJ(mubBot.misc.lockSkipped); }, 500);
                        }else{
                            API.sendChat("This command requires bouncer or higher!");
                        }
                        break;
                    case 'rvf':
                    case 'removedfilter':
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) mubBot.settings.removedFilter ? API.sendChat("Removed video filter is enabled") : API.sendChat("Removed video is disabled");
                        break;
                    case 'trvf':
                    case 'toggleremovedfilter':
                        mubBot.settings.removedFilter = !mubBot.settings.removedFilter;
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) mubBot.settings.removedFilter ? API.sendChat("Removed video filter is enabled") : API.sendChat("Removed video is disabled");
                        break;
                    case "historyfilter":
                    case "hf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) mubBot.settings.historyFilter ? API.sendChat("History filter is enabled") : API.sendChat("History filter is disabled");
                        botMethods.save();
                        break;
 
                    case "swearfilter":
                    case "sf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) mubBot.settings.swearFilter ? API.sendChat("Swearing filter is enabled") : API.sendChat("Swearing filter is disabled");
                        botMethods.save();
                        break;
 
                    case "racismfilter":
                    case "rf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) mubBot.settings.racismFilter ? API.sendChat("Racism filter is enabled") : API.sendChat("Racism filter is disabled");
                        botMethods.save();
                        break;
 
                    case "beggerfilter":
                    case "bf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1) mubBot.settings.beggerFilter ? API.sendChat("Begger filter is enabled") : API.sendChat("Begger filter is disabled");
                        botMethods.save();
                        break;
 
                    case "tsf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(mubBot.settings.swearFilter){
                                mubBot.settings.swearFilter = false;
                                API.sendChat("Bot will no longer filter swearing.");
                            }else{
                                mubBot.settings.swearFilter = true;
                                API.sendChat("Bot will now filter swearing.");
                            }
                        }
                        botMethods.save();
                        break;
 
                    case "trf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(mubBot.settings.racismFilter){
                                mubBot.settings.racismFilter = false;
                                API.sendChat("Bot will no longer filter racism.");
                            }else{
                                mubBot.settings.racismFilter = true;
                                API.sendChat("Bot will now filter racism.");
                            }
                        }
                        botMethods.save();
                        break;
 
                    case "tbf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(mubBot.settings.beggerFilter){
                                mubBot.settings.beggerFilter = false;
                                API.sendChat("Bot will no longer filter fan begging.");
                            }else{
                                mubBot.settings.beggerFilter = true;
                                API.sendChat("Bot will now filter fan begging.");
                            }
                        }
                        botMethods.save();
                        break;
                    case "thf":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(mubBot.settings.historyFilter){
                                mubBot.settings.historyFilter = false;!
                                    API.sendChat("Bot will no longer skip songs that are in the room history.");
                            }else{
                                mubBot.settings.historyFilter = true;
                                API.sendChat("Bot will now skip songs that are in the room history.");
                            }
                        }
                        botMethods.save();
                        break;
 
                    case "version":
                        API.sendChat("RedstonerBot  version " + mubBot.misc.version);
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "origin":
                    case "owner":
                    case "authors":
                    case "creator":
                        API.sendChat(mubBot.misc.origin);
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "status":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            var response = "";
                            var currentTime = new Date().getTime();
                            var minutes = Math.floor((currentTime - joined) / 60000);
                            var hours = 0;
                            while(minutes > 60){
                                minutes = minutes - 60;
                                hours++;
                            }
                            hours == 0 ? response = "Running for " + minutes + "m " : response = "Running for " + hours + "h " + minutes + "m";
                            response = response + " | Begger filter: "+mubBot.settings.beggerFilter;
                            response = response + " | Swear filter: "+mubBot.settings.swearFilter;
                            response = response + " | Racism filter: "+mubBot.settings.racismFilter;
                            response = response + " | History filter: "+mubBot.settings.historyFilter;
                            response = response + " | MaxLength: " + mubBot.settings.maxLength + "m";
                            response = response + " | Cooldown: " + mubBot.settings.cooldown + "s";
                            response = response + " | Removed Video Filter: "+ mubBot.settings.removedFilter;
                            API.sendChat(response);
                        }
                        break;
 
                    case "cooldown":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(typeof command[1] == "undefined"){
                                if(mubBot.settings.cooldown != 0.0001){
                                    API.sendChat('Cooldown is '+mubBot.settings.cooldown+' seconds');
                                }else{
                                    API.sendChat('Cooldown is disabled');
                                }
                            }else if(command[1] == "disable"){
                                mubBot.settings.cooldown = 0.0001;
                                API.sendChat('Cooldown disabled');
                            }else{
                                mubBot.settings.cooldown = command[1];
                                API.sendChat('New cooldown is '+mubBot.settings.cooldown+' seconds');
                            }
                        }
                        botMethods.save();
                        break;
 
                    case "maxlength":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(typeof command[1] == "undefined"){
                                if(mubBot.settings.maxLength != 1e+50){
                                    API.sendChat('Maxlength is '+mubBot.settings.maxLength+' minutes');
                                }else{
                                    API.sendChat('Maxlength is disabled');
                                }
                            }else if(command[1] == "disable"){
                                mubBot.settings.maxLength = Infinity;
                                API.sendChat('Maxlength disabled');
                            }else{
                                mubBot.settings.maxLength = command[1];
                                API.sendChat('New maxlength is '+mubBot.settings.maxLength+' minutes');
                            }
                        }
                        botMethods.save();
                        break;
 
                    case "interactive":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            mubBot.settings.interactive ? API.sendChat("Bot is interactive.") : API.sendChat("Bot is not interactive.");
                        }
                        break;
 
                    case "toggleinteractive":
                    case "ti":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            if(mubBot.settings.interactive){
                                mubBot.settings.interactive = false;
                                API.sendChat("Bot will no longer interact.");
                            }else{
                                mubBot.settings.interactive = true;
                                API.sendChat("Bot will now interact.");
                            }
                        }
                        botMethods.save();
                        break;
 
                    case "save":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            botMethods.save();
                            API.sendChat("Settings saved.");
                        }
                        break;
 
                    case "stfu":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            mubBot.settings.interactive = false;
                            API.sendChat("Yessir!");
                        }
                        botMethods.save();
                        break;
 
                    case "changelog":
                        if(API.getUser(fromID).permission > 1 || mubBot.admins.indexOf(fromID) > -1){
                            API.sendChat("New in version " + mubBot.misc.version + " - " + mubBot.misc.changelog)
                        }
                        break;
 
                }
            }
        }
    });
 
    API.on(API.CHAT, function(data){
        if(data.message.indexOf('!') === 0){
            var msg = data.message, from = data.from, fromID = data.fromID;
            var command = msg.substring(1).split(' ');
            if(typeof command[2] != "undefined"){
                for(var i = 2; i<command.length; i++){
                    command[1] = command[1] + ' ' + command[i];
                }
            }
            if(mubBot.misc.ready || mubBot.admins.indexOf(fromID) > -1 ||API.getUser(fromID).permission > 1){
                switch(command[0].toLowerCase()){
                    case "taco":
                        if(typeof command[1].indexOf("@") === 0){
                            var crowd = API.getUsers();
                            var randomUser = Math.floor(Math.random() * crowd.length);
                            var randomTaco = Math.floor(Math.random() * mubBot.misc.tacos.length);
                            var randomSentence = Math.floor(Math.random() * 3);
                            switch(randomSentence){
                                case 0:
                                    API.sendChat("@" + crowd[randomUser].username + ", take this " + mubBot.misc.tacos[randomTaco] + ", you b*tch!");
                                    break;
                                case 1:
                                    API.sendChat("@" + crowd[randomUser].username + ", quickly! Eat this " + mubBot.misc.tacos[randomTaco] + " before I do!");
                                    break;
                                case 2:
                                    API.sendChat("One free " + mubBot.misc.tacos[randomTaco] + " for you, @" + crowd[randomUser].username + ". :3");
                                    break;
                                case 3:
                                    API.sendChat("/me throws a " + mubBot.misc.tacos[randomTaco] + " at @" + crowd[randomUser].username + "!");
                                    break;
                                default:
                                    API.sendChat('Who the hell is that?');
                                    break;
                            }
                        }else{
                            if(command[1].indexOf("@") === 0) command[1] = command[1].substring(1);
                            var randomTaco = Math.floor(Math.random() * mubBot.misc.tacos.length);
                            var randomSentence = Math.floor(Math.random() * 3);
                            switch(randomSentence){
                                case 0:
                                    API.sendChat("@" + botMethods.cleanString(command[1]) + ", take this " + mubBot.misc.tacos[randomTaco] + ", you b*tch!");
                                    break;
                                case 1:
                                    API.sendChat("@" + botMethods.cleanString(command[1]) + ", quickly! Eat this " + mubBot.misc.tacos[randomTaco] + " before I do!");
                                    break;
                                case 2:
                                    API.sendChat("One free " + mubBot.misc.tacos[randomTaco] + " for you, @" + botMethods.cleanString(command[1]) + ". :3");
                                    break;
                                case 3:
                                    API.sendChat("/me throws a " + mubBot.misc.tacos[randomTaco] + " at @" + botMethods.cleanString(command[1]) + "!");
                                    break;
                                default:
                                    API.sendChat('Who the hell is that?');
                                    break;
                            }
                        }
                        if(mubBot.admins.indexOf(fromID) > -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
                    case "hug":
                        if(typeof command[1].indexOf("@") === 0){
                            var crowd = API.getUsers();
                            var randomUser = Math.floor(Math.random() * crowd.length);
                            var randomSentence = Math.floor(Math.random() * 3);
                            switch(randomSentence){
                                case 0:
                                    API.sendChat("Hugs? Forget that!");
                                    setTimeout(function(){
                                        API.sendChat("/me grabs @"+crowd[randomUser].username+"'s ass");
                                    }, 650);
                                    break;
                                case 1:
                                    API.sendChat("/me gives @"+crowd[randomUser].username+" a big bear hug");
                                    break;
                                case 2:
                                    API.sendChat("/me gives @"+crowd[randomUser].username+" a soft, furry hug");
                                    break;
                                case 3:
                                    API.sendChat("/me gives @"+crowd[randomUser].username+" an awkward hug");
                                    break;
                                default:
                                    API.sendChat('Who the hell is that?');
                                    break;
                            }
                        }else{
                            if(command[1].indexOf("@") === 0) command[1] = command[1].substring(1);
                            var crowd = API.getUsers();
                            var randomUser = Math.floor(Math.random() * crowd.length);
                            var randomSentence = Math.floor(Math.random() * 3);
                            switch(randomSentence){
                                case 0:
                                    API.sendChat("Hugs? Forget that!");
                                    setTimeout(function(){
                                        API.sendChat("/me grabs @"+botMethods.cleanString(command[1])+" 's ass");
                                    }, 650);
                                    break;
                                case 1:
                                    API.sendChat("/me gives @"+botMethods.cleanString(command[1])+" a big bear hug");
                                    break;
                                case 2:
                                    API.sendChat("/me gives @"+botMethods.cleanString(command[1])+" a soft, furry hug");
                                    break;
                                case 3:
                                    API.sendChat("/me gives @"+botMethods.cleanString(command[1])+" an awkward hug");
                                    break;
                                default:
                                    API.sendChat('Who the hell is that?');
                                    break;
                            }
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
                    case "punish":
                        if(typeof command[1].indexOf("@") === 0){
                            var crowd = API.getUsers();
                            var randomUser = Math.floor(Math.random() * crowd.length);
                            var randomSentence = Math.floor(Math.random() * 3);
                            switch(randomSentence){
                                case 0:
                                    API.sendChat("/me "+ data.from +" Starts rubbing a sandpaper on @"+crowd[randomUser].username+"'s scrotum");
                                    break;
                                case 1:
                                    API.sendChat("/me "+ data.from +" pokes @"+crowd[randomUser].username+" in the eyes.");
                                    break;
                                case 2:
                                    API.sendChat("/me "+ data.from +" throws sand in @"+crowd[randomUser].username+"'s eyes");
                                    break;
                                case 3:
                                    API.sendChat("/me "+ data.from +" makes @"+crowd[randomUser].username+"'s mother cry");
                                    break;
                                case 4:
                                    API.sendChat("/me "+ data.from +" penetrates @"+crowd[randomUser].username+" with a sharpie");
                                    break;
                                case 5:
                                    API.sendChat("/me "+ data.from +" pinches @"+crowd[randomUser].username+"'s nipples super hard");
                                    break;
                                case 6:
                                    API.sendChat("/me "+ data.from +" gives @"+crowd[randomUser].username+" a wet willy");
                                    break;
                                case 7:
                                    API.sendChat("/me "+ data.from +" lights @"+crowd[randomUser].username+" hairs on fire");
                                    break;
                                default:
                                    API.sendChat('Who the hell is that?');
                                    break;
                            }
                        }else{
                            if(command[1].indexOf("@") === 0) command[1] = command[1].substring(1);
                            var randomSentence = Math.floor(Math.random() * 3);
                            switch(randomSentence){
                                case 0:
                                    API.sendChat("/me "+ data.from +" Starts rubbing sandpaper on @"+botMethods.cleanString(command[1])+"'s scrotum");
                                    break;
                                case 1:
                                    API.sendChat("/me "+ data.from +" pokes @"+botMethods.cleanString(command[1])+" in the eyes.");
                                    break;
                                case 2:
                                    API.sendChat("/me "+ data.from +" throws sand in @"+botMethods.cleanString(command[1])+"'s eyes");
                                    break;
                                case 3:
                                    API.sendChat("/me "+ data.from +" makes @"+botMethods.cleanString(command[1])+"'s mother cry");
                                    break;
                                case 4:
                                    API.sendChat("/me "+ data.from +" penetrates @"+botMethods.cleanString(command[1])+" with a sharpie");
                                    break;
                                case 5:
                                    API.sendChat("/me "+ data.from +" pinches @"+botMethods.cleanString(command[1])+"'s nipples super hard");
                                    break;
                                case 6:
                                    API.sendChat("/me "+ data.from +" gives @"+botMethods.cleanString(command[1])+" a wet willy");
                                    break;
                                case 7:
                                    API.sendChat("/me "+ data.from +" lights @"+botMethods.cleanString(command[1])+" hairs on fire");
                                    break;
                                default:
                                    API.sendChat('Who the hell is that?');
                                    break;
                            }
                        }
                        if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                            mubBot.misc.ready = false;
                            setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                        }
                        break;
 
                    case "run":
                        if(mubBot.admins.indexOf(fromID) > -1){
                            a = botMethods.cleanString(command[1]);
                            console.log(a);
                            eval(a);
                        }
                        break;
 
                }
            }
        }
    });
 
    API.on(API.CHAT, function(data){
        if(data.message.indexOf('!rule ') === 0){
            var msg = data.message, from = data.from, fromID = data.fromID;
            var command = msg.substring(1).split(' ');
 
            if(mubBot.misc.ready || mubBot.admins.indexOf(fromID) > -1 ||API.getUser(fromID).permission > 1){
                switch(command[1]){
                    case '1':
                        API.sendChat('Don\'t Play Troll songs.');
                        break;
                    case '2':
                        API.sendChat('No songs over 5 minutes. (some songs a little bit over may be allowed, ask a mod)');
                        break;
                    case '3':
                        API.sendChat('Don\'t spam.');
                        break;
                    case '4':
                        API.sendChat('Have respect and use common sense.');
                        break;
                    case '5':
                        API.sendChat('Advertising rooms, websites, etc. without moderator approval is grounds for being kicked');
                        break;
                    case '6':
                        API.sendChat('Any song played that is currently in the history will be skipped.');
                        break;
                    case '7':
                        API.sendChat('Playing Video/season/Movies and Un-English songs, that has no effort, are subject to being skipped.');
                        break;
                    default:
                        API.sendChat('Unknown rule!');
                        break;
                }
            }
        }
    });
 
    API.on(API.CHAT, function(data){
        msg = data.message.toLowerCase(), chatID = data.chatID;
 
        for(var i = 0; i < mubBot.filters.swearWords.length; i++){
            if(msg.indexOf(mubBot.filters.swearWords[i].toLowerCase()) > -1 && mubBot.settings.swearFilter){
                API.moderateDeleteChat(chatID);
            }
        }
        for(var i = 0; i < mubBot.filters.racistWords.length; i++){
            if(msg.indexOf(mubBot.filters.racistWords[i].toLowerCase()) > -1 && mubBot.settings.racismFilter){
                API.moderateDeleteChat(chatID);
            }
        }
        for(var i = 0; i < mubBot.filters.beggerWords.length; i++){
            if(msg.indexOf(mubBot.filters.beggerWords[i].toLowerCase()) > -1 && mubBot.settings.beggerFilter){
                API.moderateDeleteChat(chatID);
            }
        }
 
    });
 
    API.on(API.CHAT, function(data){
        msg = data.message.toLowerCase(), chatID = data.chatID, fromID = data.fromID;
        if(mubBot.misc.ready || mubBot.admins.indexOf(fromID) > -1 ||API.getUser(fromID).permission > 1){
            if(msg.indexOf(':eyeroll:') > -1){
                API.sendChat('/me : ¬_¬');
                if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                    mubBot.misc.ready = false;
                    setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                }
            }
            if(msg.indexOf(':notamused:') > -1){
                API.sendChat('/me : ಠ_ಠ');
                if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                    mubBot.misc.ready = false;
                    setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                }
            }
            if(msg.indexOf(':yuno:') > -1){
                API.sendChat('/me : ლ(ಥ益ಥლ');
                if(mubBot.admins.indexOf(fromID) == -1 || API.getUser(fromID).permission < 2){
                    mubBot.misc.ready = false;
                    setTimeout(function(){ mubBot.misc.ready = true; }, mubBot.settings.cooldown * 1000);
                }
            }
        }
 
    });
 
    API.on(API.DJ_ADVANCE, DJ_ADVANCE);
    function DJ_ADVANCE(data){
        if(mubBot.settings.ruleSkip && typeof ruleSkip[data.media.id] != "undefined"){
            switch(ruleSkip[data.media.id].rule){
                case '1':
                    API.sendChat('@'+data.dj.username+' Dont play troll songs.');
                    botMethods.skip();
                    break;
                case '2':
                    API.sendChat('@'+data.dj.username+' No songs that are longer than 5 mins without permission.');
                    botMethods.skip();
                    break;
                case '3':
                    API.sendChat('@'+data.dj.username+' Song was not in English.');
                    botMethods.skip();
                    break;
                case '4':
                    API.sendChat('@'+data.dj.username+' No R34/clop/porn/gore. This includes links, songs, and chat.');
                    botMethods.skip();
                    break;
                case '5':
                    API.sendChat('@'+data.dj.username+' Don\'t Play songs that are in history!');
                    botMethods.skip();
                    break;
                case '6':
                    API.sendChat('@'+data.dj.username+' Playing Videos/season video/Movies, that has no effort are subject to being skipped');
                    botMethods.skip();
                    break;
                case '7':
                    API.sendChat('@'+data.dj.username+' Just no..');
                    botMethods.skip();
                    break;
                default:
                    API.sendChat('@'+data.dj.username+' '+ruleSkip[data.media.id].rule);
                    botMethods.skip();
                    break;
            }
        }
        $.getJSON('http://gdata.youtube.com/feeds/api/videos/'+data.media.cid+'?v=2&alt=jsonc&callback=?', function(json){response = json.data});
        setTimeout(function(){
            if(typeof response === 'undefined' && data.media.format != 2 && mubBot.settings.removedFilter){
                API.sendChat('/me This video may be unavailable!!');
                //botMethods.skip();
            }
        }, 1500);
 
        cancel = false;
    }
 
 
    botMethods.loadStorage();
    console.log("/me : Rᴇᴅsᴛᴏɴᴇʀ Script version " + mubBot.misc.version +", Reporting For DUTY!");
 
    setTimeout(function(){
        $.getScript('');
    }, 1000);
 
    setTimeout(function(){
        SC.initialize({
            client_id: 'eae62c8e7a30564e9831b9e43f1d484a'
        });
    }, 3000);
 
    API.sendChat('/me : Rᴇᴅsᴛᴏɴᴇʀ Script '+mubBot.misc.version+", Reporting For Duty!")
