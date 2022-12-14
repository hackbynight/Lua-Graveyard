# eleusian-artificial-intelligence
Collection of LUA/C automation scripts used with MUDLET


<details>
  <summary>Discord Web Hook</summary>
  
  Basic webhook to take messages given/taken via telnet and pump it into my private discord.
  
  ```lua
  function sendToDiscordWebhook(discord_username, avatarurl, message)
  local httpdone = registerAnonymousEventHandler('sysPostHttpDone', 
    function(event, rurl, response)
      if rurl == url then display(r) else return true end
    end, true)

  local httperror = registerAnonymousEventHandler('sysPostHttpError', 
    function(event, response, rurl)
      if rurl == url then display(r) else return true end
    end, true)

  local url = "https://discordapp.com/api/webhooks/975391849319641161/xVtJk-By84k1Tm9yR3Or8bdMepMInRSufOuVlkxcByX_IEK7rPHvjClFa0zMArSAah7j"
  local data = {
    username = discord_username, 
    avatar_url = avatarurl,
    content = message
  }
  local header = {
    ["Content-Type"] = "application/json",
    --["Content-Length"] = data:len()
  }
  
  postHTTP(yajl.to_string(data), url, header)
  
  killAnonymousEventHandler(httpdone)
  killAnonymousEventHandler(httperror)
end
-- 
-- function sendToDiscordWebhook(discord_username, avatarurl, message)
  -- local httpdone = registerAnonymousEventHandler('sysPostHttpDone', 
    -- function(event, rurl, response)
      -- if rurl == url then display(r) else return true end
    -- end, true)
-- 
  -- local httperror = registerAnonymousEventHandler('sysPostHttpError', 
    -- function(event, response, rurl)
      -- if rurl == url then display(r) else return true end
    -- end, true)
-- 
  -- local url = "https://discordapp.com/api/webhooks/975391849319641161/xVtJk-By84k1Tm9yR3Or8bdMepMInRSufOuVlkxcByX_IEK7rPHvjClFa0zMArSAah7j"
  -- local data = {
    -- username = discord_username, 
    -- avatar_url = avatarurl,
    -- content = message
  -- }
  -- local header = {
    -- ["Content-Type"] = "application/json",
    -- --["Content-Length"] = data:len()
  -- }
  -- 
  -- postHTTP(yajl.to_string(data), url, header)
  -- 
  -- killAnonymousEventHandler(httpdone)
  -- killAnonymousEventHandler(httperror)
-- end
  ```
  
</details>

<details>
  <summary>Initialization on Boot</summary>
  
  Starting values. Values carried over from previous sessions will have the appropriate state change. Use alias "init"
  
  ```lua
  cycle = cycle or 0
improved_serpent = improved_serpent or false
target.burrowed = target.burrowed or {}
heartbeat = heartbeat or 0
trace = trace or ""
target.plagues = target.plagues or ""
virulence_balance = true

--User Variables
user = user or {}
user.Balance = user.Balance or {}
user.room = user.room or 0
user.oldroom = user.oldroom or "none"
user.stoxin = user.shrug or "formaldehyde"
user.shrugged = user.shrugged or false
user.parrySpots = user.parrySpots or {}
user.prone = user.prone or false
user.mode = user.mode or "shadowplant"
user.weaving = user.weaving or false
user.annihilating = user.annihilating or false
user.lightwall = user.lightwall or false
user.refillTrioxin = user.refillTrioxin or false
user.devourSetup2 = false
user.devourSetup = false
user.dead = user.dead or false
user.Balance.shrugging = user.Balance.shrugging or true
user.autoyank = false
heroism = heroism or true
user.purge = user.purge or false
user.seperatar = "::"
user.wands = {"155559","60332"}
user.says = {"Burn brightly!!","You look much better on fire!!","You look a little cold, let me help you with that!!","I will snuff you like a candle!!","Fire and brimstone!!","You will rue the day you crossed paths with a Bialystone!!","The fire that burns brightest burns.. ugh just die already!!","Is it just me or is it getting hot in here?!","Can you take the scathing heat!?","We shall see which lasts longer! My flames or your resolve!!","Resistance is futile! Die like the dog you are!!","Die in a fire!!"}
user.shopad = {"You deserve it.","Aetherius' best kept secret. Tell your friends not to keep it.","Stylish and utilitarian.","Made for you.","Do you see yourself here?","For the enlightened.","There's nothing better.","What can compare?","Make a wish.","For all your needs.","Show him you care.","Show her you care.","Get inspired.","Be original.","Make your own style.","Exactly what you are looking for.","Find yourself."}
user.shoppiecesad = {""}

--Target Variables
target = target or {}
target.name = target.name or "None"
target.aura = target.aura or false
target.engulfed = target.engulfed or false
target.shield = target.shield or false
target.fenugreek = target.fenugreek or false
target.obscured = target.obscured or false
target.hypnotised = target.hypnotised or false
target.confounded = target.confounded or false
target.becloud = target.becloud or false
target.instilled = target.instilled or false
target.instilled2 = target.instilled2 or false
target.instilled3 = target.instilled3 or false
target.afflictions = target.afflictions or {}
target.hypnoaffs = target.hypnoaffs or {}
target.mentalaffs = target.mentalaffs or {}
target.herbBalance = target.herbBalance or true
target.salveBalance = target.salveBalance or true
target.smokeBalance = target.smokeBalance or true
target.herb = target.herb or "none"
target.smoke = target.pipeSmoked or "none"
target.paralysis = target.paralysis or false
target.scourged = target.scourged or false
target.tree = target.tree or true
target.purge = target.purge or true
target.obstruct = target.obstruct or false

--Toxin Variables
Toxin = Toxin or {}
Toxin.All =Toxin.All or {}
Toxin.Kelp = Toxin.Kelp or {}
Toxin.Oxalis = Toxin.Oxalis or {}
Toxin.Kelp2 = Toxin.Kelp2 or {}
Toxin.Maidenhair = Toxin.Maidenhair or {}
Toxin.Lock = Toxin.Lock or {}
Toxin.Salve = Toxin.Salve or {}
Toxin.Evileye = Toxin.Evileye or {}
Toxin.dsl = Toxin.dsl or {}
Toxin.stab = Toxin.stab or {}
Toxin.Lock.queue = {"iodine","calotropis","bromine","aconite","atropine","mebaral"}
Toxin.Kelp.queue = {"hemotoxin"}
Toxin.Kelp2.queue = {"hemotoxin"}
Toxin.Maidenhair.queue = {"ciguatoxin","metrazol","deaf","butisol","xeroderma","mercury","strychnine"}
Toxin.All.queue = {"vitriol","chloroform","noctec","mebaral","benzene2","benzedrine2","opium","opium"}
Toxin.Salve.queue = {"hemotoxin","butisol","oxalis","strychnine","metrazol","ciguatoxin","opium","opium"}
Toxin.Oxalis.queue = {"oxalis","hemotoxin","butisol","ciguatoxin","metrazol","strychnine","mercury","iodine","calotropis","bromine","benzene2","benzedrine2","aconite","atropine","mazanor","opium","opium"}

--Misc Variables
guards = guards or true
timewarptime = timewarptime or false
timewarping = timewarping or false
devilwindow = devilwindow or "false"
markedwindow = markedwindow or "none"
undeadwindow = undeadwindow or "none"
Team = Team or {}
standguard = false
openURL = openURL
landsense = landsense or false
Team.Brazier = Team.Brazier or false
getbody = false
afk = false
allallies = false
scenttargets = {}
bloodscent = bloodscent or false
bloodscenttargets = {}
bloodscentleft = bloodscentleft or false
bloodscententered = bloodscententered or false
shardhealcd = shardhealcd or false
windowexits = windowexits or "none"
raidwindow = raidwindow or "false"
timewarpwindow = timewarpwindow or "false"
guardswindow = guardswindow or "false"
whohere = whohere or "none"
guiDrawn = guiDrawn or false
Wielded = Wielded or {"dirk","shield"}
highlighttimerroom = highlighttimerroom or "0"
daeggerhunttoxin = daeggerhunttoxin or "none"
catharsismode = catharsismode or false
target.infirmity = target.infirmity  or 0
kelpinfirmity = kelpinfirmity or false
nightshadeinfirmity = nightshadeinfirmity or false
mandrakeinfirmity = mandrakeinfirmity or false
wormwoodinfirmity = wormwoodinfirmity or false
orphineinfirmity = orphineinfirmity or false
doppieroom = doppieroom or false
instatar = instatar or "none"
engage = engage or false
DSLTIME = DSLTIME or false
limbcountrightarm = limbcountrightarm or 0
limbcountleftarm = limbcountleftarm or 0
limbcountrightleg = limbcountrightleg or 0
limbcountleftleg = limbcountleftleg or 0
limbcounthead = limbcounthead or 0
onethirdsmark = onethirdmark or 0
twothirdsmark = twothirdmark or 0
mutilatedmark = mutilatedmark or 0
lynxsurge = lynxsurge or false
leopardsurge = leopardsurge or false
wolverinesurge = wolverinesurge or false
freezeblood = freezeblood or 0
freezebloodwindow = 0
frostheart = frostheart or false
soulquenchclaymore = false
soulquenchsabre = false
bloodfreezewindow = bloodfreezewindow or 0
lastaff = lastaff or "none"
soulquench = soulquench or false
dryblood = dryblood or false
suggestkick = suggestkick or false
analysetable = analysetable or {}
Newanalysetable = Newanalysetable or {}
reraid = reraid or false
reguard = reguard or false
opendoor = opendoor or false
boardpiece = boardpiece or "none"
jump = jump or false
memory = memory or false
goldtracked = goldtracked or 0
dslaffs = dslaffs or {}
dslaffprompt = dslaffprompt or false
shadowplanted = shadowplanted or "none"
shieldtouch = shieldtouch or false
mobnumber = mobnumber or 0
chopper = chopper or false
lastevisc = atropine or "none"

--Bard Variables
revelation = revelation or false
dissonance = dissonance or false
lullaby = lullaby or false
bedazzlement = bedazzlement or false
yoth = yoth or false
nuarinyuaff = nuarinyuaff or "none"
juggleaxe = juggleaxe or "none"
juggle1 = juggle1 or false
juggle2 = juggle2 or false
juggle3 = juggle3 or false
axeenvenom = axeenvenom or "none"
sketchlink = sketchlink or true
ta = ta or false
myried = myried or false
huntingdelay = huntingdelay or false
huntingnumber = huntingnumber or 0
class = class or "none"
lastknownlocation = lastknownlocation or "none"
lastknownperson = lastknownperson or "none"
roomnumberset = roomnumberset or {}
unleashdemon = unleashdemon or "none"

--Saboteur Variables
Shadowplant = Shadowplant or {}
Worm = Worm or {}
Wielded = Wielded or {"dirk","shield"}
trioxinFill = trioxinFill or 0
kelp_affs = kelp_affs or 0
m_affs = m_affs or 0
analyser = analyser or false
toxins = toxins or {}
hypnoadd = hypnoadd or 1
hypnowrithe = hypnowrithe or 1
hypnosnap = hypnosnap or false
Hypnosislockqueue = {"Impatience","Hypochondria","Deadening","Hypersomnia"}
Hypnosiswrithequeue = {"action probe lightwall","action writhe","action writhe","action writhe","action writhe","action writhe","action writhe","action writhe","action writhe","action writhe","action writhe"}
Worm.Attached = Worm.Attached or false
Worm.Environment = Worm.Environment or "none"
Worm.Summon = Worm.Summon or false
Shadowplant.planted = Shadowplant.planted or false
Shadowplant.plant = Shadowplant.plant or "kelp"
pesalve = false
restorationdevour = false

--Pariah Variables
epitaphcount = 0
epitaphdelay = 0
epitaph = ""
swarmcommand = ""
plague = ""
exposed = false
logograph = ""

--Parry Variables
rightarmbruise = rightarmbruise or false
leftarmbruise = leftarmbruise or false
rightlegbruise = rightlegbruise or false
leftlegbruise = leftlegbruise or false

--Wytch Variables
aeon = false
nairatblock = 0
loshreblock = 0
tree = tree or true
kena = kena or false
mannaz = mannaz or false
loshre = loshre or false
nairat = nairat or false
hugalaz = hugalaz or false
inguz = inguz or false

Allies = {
Tag = true,
Septus = true,
Arakis = true,
Dreacor = true,
Jules = true,
Khizan = true,
Kryss = true,
Septus = true,
Shou = true,
Theophilus = true,
Ultrix = true,
Xeron = true,
Raini = true,
Arcarn = true,
Celestine = true,
}

nooseexits = {
"n",
"ne",
"e",
"se",
"s",
"sw",
"w",
"nw",
"up",
"d",
"in",
"out",
}

namelist = {"Millbei",
"Rasu",
"Desiray",
"Aelhion",
"Lochlann",
"Kazandar",
"Asamira",
"Haruhara",
"Ranara",
"Makog",
"Molli",
"Riayu",
"Vyla",
"Shanto",
"Aprum",
"Erick",
"Taurnil",
"Lhachman",
"Jover",
"Krystie",
"Marron",
"Lizue",
"Flaerian",
"Kogoeru",
"Rynia",
"Iva",
"Vierce",
"Xeroth",
"Lacynos",
"Riahn",
"Lystet",
"Zelon",
"Nigend",
"Sakara",
"Aidun",
"Isandria",
"Teine",
"Elinia",
"Arilanue",
"Kelstaadith",
"Dily",
"Everleigh",
"Moranda",
"Emalyna",
"Farran",
"Lelina",
"Kaishi",
"Celadriel",
}

snapnamelist = {
"Insidian",
"Suspira",
"Rynok",
"Alakshmi",
}

-- config prompt custom H:*h(%h%) M:*m(%m%) <*b*I> *B *w

  ```
  
</details>

<details>
  <summary>Summoner Function "Creator"</summary>
  
  ```lua
  function Noctu(type)
	if type == "impatience" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceduplicate" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion duplicate")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " duplicate")
		end	
	elseif type == "impatienceduplicatepyradius" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor quicken at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
--		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon quicken at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon quicken at " .. target.name .. " confusion duplicate")
		else
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceblast" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " blast")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name.. " blast")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " blast")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " stupidity blast")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism blast")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity blast")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism blast")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion blast")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " blast")
		end	
	elseif type == "impatiencestrengthen" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " strengthen")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " strenghten")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " strengthen")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity strengthen")
--		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " heroism strengthen")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " strengthen")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity strengthen")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism strengthen")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion strengthen")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " strengthen")
		end	
	elseif type == "vertigo" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice disorient at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name .. " strengthen")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon disorient at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice disorient at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator disorient at " .. target.name)
		end	
	elseif type == "epilepsy" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice flash at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor flash at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon flash at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice flash at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name)
		end	
	elseif type == "epilepsyduplicate" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice flash at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor flash at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon flash at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " confusion duplicate")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name .. " duplicate")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulwrackduplicate" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon soulwrack at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " confusion duplicate")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name .. " duplicate")
		end	
	elseif type == "vertigointensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice disorient at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon disorient at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice disorient at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator disorient at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinationsintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("nocadmus")
			send("fling justice fever at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("nocadmus")
			send("fling hierophant fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("nocadmus")
			send("fling emperor fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("nocadmus")
			send("fling justice fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " confusion intensify")
		else
			Unleash("nocadmus")
			send("fling creator fever at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinations" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("nocadmus")
			send("fling justice fever at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("nocadmus")
			send("fling hierophant fever at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("nocadmus")
			send("fling emperor fever at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("nocadmus")
--			send("fling moon fever at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("nocadmus")
			send("fling justice fever at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " confusion")
		else
			Unleash("nocadmus")
			send("fling creator fever at " .. target.name)
		end	
	elseif type == "deafintensify" then
		if (not table.contains(target.afflictions, "justice")) then
			send("fling justice nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor nervewrack at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon nervewrack at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon nervewrack at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			send("fling justice nervewrack at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon nervewrack at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon nervewrack at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon nervewrack at " .. target.name .. " confusion intensify")
		else
			send("fling creator neverwrack at " .. target.name .. " intensify")
		end	
	elseif type == "deafintensifycreator" then
		if (not table.contains(target.afflictions, "justice")) then
			send("fling justice nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor nervewrack at " .. target.name.. " intensify")
		else
			send("fling creator nervewrack at " .. target.name .. " intensify")
		end	
	elseif type == "deaf" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice nervewrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nervewrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor nervewrack at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon nervewrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice nervewrack at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator nervewrack at " .. target.name)
		end	
	elseif type == "heave" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor heave at " .. target.name.. " intensify")
			Unleash("heave")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon heave at " .. target.name .. " stupidity intensify")
--			Unleash("heave")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon heave at " .. target.name .. " heroism intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "justice")) then
			send("fling justice heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon heave at " .. target.name .. " stupidity intensify")
			Unleash("heave")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon heave at " .. target.name .. " masochism intensify")
			Unleash("heave")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon heave at " .. target.name .. " confusion intensify")
			Unleash("heave")
		else
			send("fling creator quicken at " .. target.name .. " intensify")
			Unleash("heave")
		end	
	elseif type == "heaveho" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice heave at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant heave at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")		
			send("fling emperor heave at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon heave at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice heave at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator heave at " .. target.name)
		end	
	elseif type == "soulburn" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice soulburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor soulburn at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon soulburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice soulburn at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator soulburn at " .. target.name)
		end	
	elseif type == "healthleech" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon dehydrate at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name)
		end	
	elseif type == "healthleechintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name .. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon dehydrate at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
  			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
  			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name .. " intensify")
		end	
	elseif type == "nerveburn" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon nerveburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name)
		end	
	elseif type == "nerveburnintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon nerveburn at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name .. " intensify")
		end	
	elseif type == "asthma" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice combustion at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant combustion at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor combustion at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon combustion at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice combustion at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon combustino at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator combustion at " .. target.name .. " intensify")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor soulwrack at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon soulwrack at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "justice")) then
			send("fling justice soulwrack at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon soulwrack at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon soulwrack at " .. target.name .. " confusion")
		else
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulburnblast" then
		send("invoke soulburn " .. target.name .. " blast")
	end
end

function Creator(type)
	if type == "impatience" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceduplicate" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " duplicate")
		end	
	elseif type == "impatienceduplicatepyradius" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		else
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceblast" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " blast")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name.. " blast")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " blast")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " blast")
		end	
	elseif type == "impatiencestrengthen" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " strengthen")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " strenghten")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " strengthen")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism strengthen")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " strengthen")
		end	
	elseif type == "vertigo" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice disorient at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name .. " strengthen")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism")
			Unleash("affliction")
			send("fling creator disorient at " .. target.name)
		end	
	elseif type == "epilepsy" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice flash at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor flash at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name)
		end	
	elseif type == "epilepsyduplicate" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice flash at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism duplicate")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name .. " duplicate")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulwrackduplicate" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism duplicate")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name .. " duplicate")
		end	
	elseif type == "vertigointensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice disorient at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator disorient at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinationsintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice fever at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon fever at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator fever at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinations" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice fever at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant fever at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor fever at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon fever at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator fever at " .. target.name)
		end	
	elseif type == "deafintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor nervewrack at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon nervewrack at " .. target.name .. " heroism intensify")
		else
			send("fling creator neverwrack at " .. target.name .. " intensify")
		end	
	elseif type == "deafintensifycreator" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor nervewrack at " .. target.name.. " intensify")
		else
			send("fling creator nervewrack at " .. target.name .. " intensify")
		end	
	elseif type == "deaf" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice nervewrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nervewrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor nervewrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator nervewrack at " .. target.name)
		end	
	elseif type == "heave" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor heave at " .. target.name.. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon heave at " .. target.name .. " heroism intensify")
			Unleash("heave")
		else
			send("fling creator quicken at " .. target.name .. " intensify")
			Unleash("heave")
		end	
	elseif type == "heaveho" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice heave at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant heave at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")		
			send("fling emperor heave at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator heave at " .. target.name)
		end	
	elseif type == "soulburn" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice soulburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor soulburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator soulburn at " .. target.name)
		end	
	elseif type == "healthleech" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name)
		end	
	elseif type == "healthleechintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
  			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name .. " intensify")
		end	
	elseif type == "nerveburn" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name)
		end	
	elseif type == "nerveburnintensify" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name .. " intensify")
		end	
	elseif type == "asthma" then
		if (not table.contains(target.afflictions, "justice"))  then
			Unleash("affliction")
			send("fling justice combustion at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant combustion at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			Unleash("affliction")
			send("fling emperor combustion at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator combustion at " .. target.name .. " intensify")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "justice"))  then
			send("fling justice soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "numbness")) then		
			send("fling emperor soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon soulwrack at " .. target.name .. " heroism")
		else
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulburnblast" then
		send("invoke soulburn " .. target.name .. " blast")
	end
end
  ```
  
</details>

<details>
  <summary>Summoner Function "Heroism"</summary>
    
  ```lua
  function Noctu(type)
	if type == "impatience" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceduplicate" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion duplicate")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " duplicate")
		end	
	elseif type == "impatienceduplicatepyradius" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice quicken at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
--		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon quicken at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon quicken at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon quicken at " .. target.name .. " confusion duplicate")
		else
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceblast" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " blast")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name.. " blast")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " blast")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " stupidity blast")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism blast")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity blast")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism blast")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion blast")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " blast")
		end	
	elseif type == "impatiencestrengthen" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " strengthen")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " strenghten")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " strengthen")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity strengthen")
--		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
--			Unleash("affliction")
--			send("fling moon quicken at " .. target.name .. " heroism strengthen")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " strengthen")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " stupidity strengthen")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " masochism strengthen")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " confusion strengthen")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " strengthen")
		end	
	elseif type == "vertigo" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice disorient at " .. target.name .. " strengthen")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon disorient at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator disorient at " .. target.name)
		end	
	elseif type == "epilepsy" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor flash at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice flash at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon flash at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor flash at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name)
		end	
	elseif type == "epilepsyduplicate" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor flash at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice flash at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon flash at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " confusion duplicate")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name .. " duplicate")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulwrackduplicate" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name .. " duplicate")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon soulwrack at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name .. " heroism duplicate")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " stupidity duplicate")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " masochism duplicate")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " confusion duplicate")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name .. " duplicate")
		end	
	elseif type == "vertigointensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice disorient at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon disorient at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator disorient at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinationsintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("nocadmus")
			send("fling emperor fever at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("nocadmus")
			send("fling hierophant fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("nocadmus")
			send("fling justice fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("nocadmus")
			send("fling emperor fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " confusion intensify")
		else
			Unleash("nocadmus")
			send("fling creator fever at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinations" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("nocadmus")
			send("fling emperor fever at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("nocadmus")
			send("fling hierophant fever at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("nocadmus")
			send("fling justice fever at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("nocadmus")
--			send("fling moon fever at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("nocadmus")
			send("fling emperor fever at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("nocadmus")
			send("fling moon fever at " .. target.name .. " confusion")
		else
			Unleash("nocadmus")
			send("fling creator fever at " .. target.name)
		end	
	elseif type == "deafintensify" then
		if (not table.contains(target.afflictions, "numbness")) then
			send("fling emperor nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice nervewrack at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon nervewrack at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon nervewrack at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			send("fling emperor nervewrack at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon nervewrack at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon nervewrack at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon nervewrack at " .. target.name .. " confusion intensify")
		else
			send("fling creator neverwrack at " .. target.name .. " intensify")
		end	
	elseif type == "deafintensifycreator" then
		if (not table.contains(target.afflictions, "numbness")) then
			send("fling emperor nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice nervewrack at " .. target.name.. " intensify")
		else
			send("fling creator nervewrack at " .. target.name .. " intensify")
		end	
	elseif type == "deaf" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor nervewrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nervewrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice nervewrack at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon nervewrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor nervewrack at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator nervewrack at " .. target.name)
		end	
	elseif type == "heave" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice heave at " .. target.name.. " intensify")
			Unleash("heave")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon heave at " .. target.name .. " stupidity intensify")
--			Unleash("heave")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon heave at " .. target.name .. " heroism intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "numbness")) then
			send("fling emperor heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon heave at " .. target.name .. " stupidity intensify")
			Unleash("heave")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon heave at " .. target.name .. " masochism intensify")
			Unleash("heave")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon heave at " .. target.name .. " confusion intensify")
			Unleash("heave")
		else
			send("fling creator quicken at " .. target.name .. " intensify")
			Unleash("heave")
		end	
	elseif type == "heaveho" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor heave at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant heave at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")		
			send("fling justice heave at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon heave at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor heave at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator heave at " .. target.name)
		end	
	elseif type == "soulburn" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor soulburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice soulburn at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon soulburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor soulburn at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator soulburn at " .. target.name)
		end	
	elseif type == "healthleech" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon dehydrate at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name)
		end	
	elseif type == "healthleechintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name .. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon dehydrate at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
  			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
  			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name .. " intensify")
		end	
	elseif type == "nerveburn" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon nerveburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " confusion")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name)
		end	
	elseif type == "nerveburnintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon nerveburn at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name .. " intensify")
		end	
	elseif type == "asthma" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor combustion at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant combustion at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice combustion at " .. target.name.. " intensify")
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			Unleash("affliction")
--			send("fling moon combustion at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " heroism intensify")
		elseif (not table.contains(target.afflictions, "numbness")) then
			Unleash("affliction")
			send("fling emperor combustion at " .. target.name .. " intensify")
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " stupidity intensify")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " masochism intensify")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			Unleash("affliction")
			send("fling moon combustino at " .. target.name .. " confusion intensify")
		else
			Unleash("affliction")
			send("fling creator combustion at " .. target.name .. " intensify")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice soulwrack at " .. target.name)
--		elseif (not table.contains(target.mentalaffs, "stupidity")) then
--			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon soulwrack at " .. target.name .. " heroism")
		elseif (not table.contains(target.afflictions, "numbness")) then
			send("fling emperor soulwrack at " .. target.name)
		elseif (not table.contains(target.mentalaffs, "stupidity")) then
			send("fling moon soulwrack at " .. target.name .. " stupidity")
		elseif (not table.contains(target.mentalaffs, "masochism")) then
			send("fling moon soulwrack at " .. target.name .. " masochism")
		elseif (not table.contains(target.mentalaffs, "confusion")) then
			send("fling moon soulwrack at " .. target.name .. " confusion")
		else
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulburnblast" then
		send("invoke soulburn " .. target.name .. " blast")
	end
end

function Creator(type)
	if type == "impatience" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceduplicate" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " duplicate")
		end	
	elseif type == "impatienceduplicatepyradius" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor quicken at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice quicken at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon quicken at " .. target.name .. " heroism duplicate")
		else
			send("fling creator quicken at " .. target.name)
		end	
	elseif type == "impatienceblast" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " blast")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name.. " blast")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " blast")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " blast")
		end	
	elseif type == "impatiencestrengthen" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor quicken at " .. target.name .. " strengthen")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant quicken at " .. target.name .. " strenghten")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice quicken at " .. target.name .. " strengthen")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon quicken at " .. target.name .. " heroism strengthen")
		else
			Unleash("affliction")
			send("fling creator quicken at " .. target.name .. " strengthen")
		end	
	elseif type == "vertigo" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice disorient at " .. target.name .. " strengthen")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism")
			Unleash("affliction")
			send("fling creator disorient at " .. target.name)
		end	
	elseif type == "epilepsy" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor flash at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice flash at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name)
		end	
	elseif type == "epilepsyduplicate" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor flash at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice flash at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon flash at " .. target.name .. " heroism duplicate")
		else
			Unleash("affliction")
			send("fling creator flash at " .. target.name .. " duplicate")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulwrackduplicate" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor soulwrack at " .. target.name .. " duplicate")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulwrack at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice soulwrack at " .. target.name .. " duplicate")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulwrack at " .. target.name .. " heroism duplicate")
		else
			Unleash("affliction")
			send("fling creator soulwrack at " .. target.name .. " duplicate")
		end	
	elseif type == "vertigointensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor disorient at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant disorient at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice disorient at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon disorient at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator disorient at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinationsintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor fever at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice fever at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon fever at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator fever at " .. target.name .. " intensify")
		end	
	elseif type == "hallucinations" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor fever at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant fever at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice fever at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon fever at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator fever at " .. target.name)
		end	
	elseif type == "deafintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice nervewrack at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon nervewrack at " .. target.name .. " heroism intensify")
		else
			send("fling creator neverwrack at " .. target.name .. " intensify")
		end	
	elseif type == "deafintensifycreator" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor nervewrack at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant nervewrack at " .. target.name .." intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice nervewrack at " .. target.name.. " intensify")
		else
			send("fling creator nervewrack at " .. target.name .. " intensify")
		end	
	elseif type == "deaf" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor nervewrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nervewrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice nervewrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nervewrack at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator nervewrack at " .. target.name)
		end	
	elseif type == "heave" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant heave at " .. target.name .. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice heave at " .. target.name.. " intensify")
			Unleash("heave")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon heave at " .. target.name .. " heroism intensify")
			Unleash("heave")
		else
			send("fling creator quicken at " .. target.name .. " intensify")
			Unleash("heave")
		end	
	elseif type == "heaveho" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor heave at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant heave at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")		
			send("fling justice heave at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon heave at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator heave at " .. target.name)
		end	
	elseif type == "soulburn" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor soulburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant soulburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then
			Unleash("affliction")
			send("fling justice soulburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon soulburn at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator soulburn at " .. target.name)
		end	
	elseif type == "healthleech" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name)
		end	
	elseif type == "healthleechintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor dehydrate at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice dehydrate at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
  			Unleash("affliction")
			send("fling moon dehydrate at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator dehydrate at " .. target.name .. " intensify")
		end	
	elseif type == "nerveburn" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name)
		end	
	elseif type == "nerveburnintensify" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor nerveburn at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant nerveburn at " .. target.name .. " intensify")
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice nerveburn at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon nerveburn at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator nerveburn at " .. target.name .. " intensify")
		end	
	elseif type == "asthma" then
		if (not table.contains(target.afflictions, "numbness"))  then
			Unleash("affliction")
			send("fling emperor combustion at " .. target.name .. " intensify")
		elseif target.hierophant == false and hierophantmode == true then		
			Unleash("affliction")
			send("fling hierophant combustion at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			Unleash("affliction")
			send("fling justice combustion at " .. target.name.. " intensify")
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			Unleash("affliction")
			send("fling moon combustion at " .. target.name .. " heroism intensify")
		else
			Unleash("affliction")
			send("fling creator combustion at " .. target.name .. " intensify")
		end	
	elseif type == "soulwrack" then
		if (not table.contains(target.afflictions, "numbness"))  then
			send("fling emperor soulwrack at " .. target.name)
		elseif target.hierophant == false and hierophantmode == true then		
			send("fling hierophant soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "justice")) then		
			send("fling justice soulwrack at " .. target.name)
		elseif (not table.contains(target.afflictions, "heroism")) and heroism == true then
			send("fling moon soulwrack at " .. target.name .. " heroism")
		else
			send("fling creator soulwrack at " .. target.name)
		end	
	elseif type == "soulburnblast" then
		send("invoke soulburn " .. target.name .. " blast")
	end
end
  ```
  
</details>

<details>
  <summary>Affliction Handling</summary>
  
  Guard handling a separate function
  
  ```lua
  --Affliction functions
aff = aff or "none"
guards = guards or false

function targAffAdd(aff)
  cecho("<white>[<blue>Tracking<white>] <green>+<purple>" .. aff .. " <grey>(physical)")
	table.insert(target.afflictions, aff)
	-- if guards then
		-- send("pt " .. target.name .. " gained physical aff " .. aff, false)
	-- end
end

function targAffSub(aff)
	for k, v in ipairs (target.afflictions) do
		if (v == aff) then
      cecho("<white>[<blue>Tracking<white>] <red>-<purple>" .. aff .. " <grey>(physical)")
			table.remove(target.afflictions, k)
     -- send("pt " .. target.name .. " cured physical aff " .. aff, false)
			break
		end
	end
end

function targMaffAdd(aff)
  cecho("<white>[<blue>Tracking<white>] <green>+<purple>" .. aff .. " <grey>(mental)")
	table.insert(target.mentalaffs, aff)
	-- if guards then
		-- send("pt " .. target.name .. " gained mental aff " .. aff, false)
	-- end
end

function targMaffSub(aff)
	for k, v in ipairs (target.mentalaffs) do
		if (v == aff) then
      cecho("<white>[<blue>Tracking<white>] <red>-<purple>" .. aff .. " <grey>(mental)")
			table.remove(target.mentalaffs, k)
      -- send("pt " .. target.name .. " cured mental aff " .. aff, false)
			break
		end
	end
end

function targPlagueAdd(aff)
  cecho("<white>[<blue>Tracking<white>] <green>+<purple>" .. aff .. " <grey>(plague)")
	table.insert(target.plagues, aff)
	-- if guards then
		-- send("pt " .. target.name .. " gained plague aff " .. aff, false)
	-- end
end

function targPlagueSub(aff)
	for k, v in ipairs (target.plagues) do
		if (v == aff) then
      cecho("<white>[<blue>Tracking<white>] <red>-<purple>" .. aff .. " <grey>(plague)")
			table.remove(target.plagues, k)
     -- send("pt " .. target.name .. " cured plague aff " .. aff, false)
			break
		end
	end
end
  ```
  
</details>

<details>
  <summary>Doublestab Function</summary>
  
  Saboteur Only - no Achaean Serp 
  
  ```lua
  --Dstab Functions
function Dstab(type)
	if (type == "shadowplant") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant " .. target.name .. " " .. Shadowplant.plant)
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant " .. target.name .. " " .. Shadowplant.plant)
		end
	elseif (type == "suggest") then
		if Wielded[1] ~= "dirk" then
			if (hypnoadded > #Hypnosislockqueue) then
				hypnoadded = 1
				send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::seal " .. target.name .. " 1")
				sysEcho("Problem 1")
			else
				send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::suggest " .. target.name .. " " .. Hypnosislockqueue[hypnoadded])
				sysEcho("Problem 2")
			end
		else
			if (hypnoadded > #Hypnosislockqueue) then
				hypnoadded = 1
				send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::seal " .. target.name .. " 1")
				sysEcho("Problem 3")
			else
				send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::suggest " .. target.name .. " " .. Hypnosislockqueue[hypnoadded])
				sysEcho("Problem 4")
			end
		end
	elseif (type == "hypnotise") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::hypnotise " .. target.name)
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::hypnotise " .. target.name)
		end
	elseif (type == "whisper") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::suggest " .. target.name .. " whisper")
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::suggest " .. target.name .. " whisper")
		end
	elseif (type == "attack") then
		if (Worm.Environment == "volcano") then
			if Wielded[1] ~= "dirk" then
				send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm burn " .. target.name)
			else
				send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm burn " .. target.name)
			end				
		elseif (Worm.Environment == "tundra") then
			if Wielded[1] ~= "dirk" then
				send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm freeze " .. target.name)
			else
				send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm freeze " .. target.name)
			end		
		elseif (Worm.Environment == "rashirmir") then
			if Wielded[1] ~= "dirk" then
				send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm choke " .. target.name)
			else
				send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm choke " .. target.name)
			end		
		elseif (Worm.Environment ~= "volcano" or Worm.Environment ~= "tundra" or Worm.Environment ~= "rashirmir") then
			send("worm create::worm attach volcano")
		end
	elseif (type == "snap attack") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant " .. target.name .. " " .. Shadowplant.plant)
		else
			send("snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant " .. target.name .. " " .. Shadowplant.plant)
		end
	elseif (type == "snap tumble") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::snap "..target.name.."::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant "..target.name.." hyssop")
		else
			send("snap "..target.name.."::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant "..target.name.." hyssop")
		end
	elseif (type == "oxalis") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " oxalis ciguatoxin::worm shadowplant "..target.name.." hyssop")
		else
			send("dstab " .. target.name .. " oxalis ciguatoxin::worm shadowplant "..target.name.." hyssop")
		end
	elseif (type == "seal") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::seal " .. target.name .. " 1")
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::seal " .. target.name .. " 1")
		end
	elseif (type == "snap confound") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm confound " .. target.name)
		else
			send("snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm confound " .. target.name)
		end
	elseif (type == "confound") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm confound " .. target.name)
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm confound " .. target.name)
		end
	elseif (type == "becloud") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm becloud " .. target.name)
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm becloud " .. target.name)
		end
	elseif (type == "snap instill") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " hypochondria")
		else
			send("snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " hypochondria")
		end
	elseif (type == "instill") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " hypochondria")
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " hypochondria")
		end
	elseif (type == "instill2") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " hemotoxin")
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " hemotoxin")
		end
	elseif (type == "instill3") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " iodine")
		else
			send("dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm instill " .. target.name .. " iodine")
		end
	elseif (type == "snap shadowplant") then
		if Wielded[1] ~= "dirk" then
			send("quickdraw dirk shield::snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant " .. target.name .. " " .. Shadowplant.plant)
		else
			send("snap " .. target.name .. "::dstab " .. target.name .. " " .. toxins[1] .. " " .. toxins[2] .. "::worm shadowplant " .. target.name .. " " .. Shadowplant.plant)
		end
	elseif (type == "deadeye") then
			if (not table.contains(target.afflictions, "hemotoxin")) then
				daeggerhunttoxin = "hemotoxin"
				send("daegger hunt " .. target.name .. " hemotoxin")
			elseif (not table.contains(target.afflictions, "butisol")) then
				daeggerhunttoxin = "butisol"
				send("daegger hunt " .. target.name .. " butisol")
			elseif (not table.contains(target.afflictions, "xeroderma")) then
				daeggerhunttoxin = "xeroderma"
				send("daegger hunt " .. target.name .. " xeroderma")
			elseif (not table.contains(target.afflictions, "iodine")) and (table.contains(target.afflictions, "asthma") and table.contains(target.afflictions, "numbness")) then
				daeggerhunttoxin = "iodine"
				send("daegger hunt " .. target.name .. " iodine")
			elseif (not table.contains(target.afflictions, "calotropis") and table.contains(target.afflictions, "iodine")) then
				daeggerhunttoxin = "calotropis"
				send("daegger hunt " .. target.name .. " calotropis")
			elseif (not table.contains(target.afflictions, "metrazol")) then
				daeggerhunttoxin = "metrazol"
				send("daegger hunt " .. target.name .. " metrazol")
			elseif (not table.contains(target.afflictions, "noctec")) then
				daeggerhunttoxin = "noctec"
				send("daegger hunt " .. target.name .. " noctec")
			elseif (not table.contains(target.afflictions, "mebaral")) then
				daeggerhunttoxin = "mebaral"
				send("daegger hunt " .. target.name .. " mebaral")
			elseif (not table.contains(target.afflictions, "crippled left arm") and not table.contains(target.afflictions, "crippled right arm")) then
				daeggerhunttoxin = "benzene"
				send("daegger hunt " .. target.name .. " benzene")
			elseif (not table.contains(target.afflictions, "crippled left leg") and not table.contains(target.afflictions, "crippled right leg")) then
				daeggerhunttoxin = "benzedrine"
				send("daegger hunt " .. target.name .. " benzedrine")
			end
		send("order loyals kill " .. target.name .. "::contemplate " .. target.name .. "::deadeyes " .. target.name .. " " .. toxins[1] .. " " .. toxins[2])
	elseif (type == "breach") then
			if (not table.contains(target.afflictions, "hemotoxin")) then
				daeggerhunttoxin = "hemotoxin"
				send("daegger hunt " .. target.name .. " hemotoxin")
			elseif (not table.contains(target.afflictions, "butisol")) then
				daeggerhunttoxin = "butisol"
				send("daegger hunt " .. target.name .. " butisol")
			elseif (not table.contains(target.afflictions, "xeroderma")) then
				daeggerhunttoxin = "xeroderma"
				send("daegger hunt " .. target.name .. " xeroderma")
			elseif (not table.contains(target.afflictions, "iodine")) and (table.contains(target.afflictions, "asthma") and table.contains(target.afflictions, "numbness")) then
				daeggerhunttoxin = "iodine"
				send("daegger hunt " .. target.name .. " iodine")
			elseif (not table.contains(target.afflictions, "calotropis") and table.contains(target.afflictions, "iodine")) then
				daeggerhunttoxin = "calotropis"
				send("daegger hunt " .. target.name .. " calotropis")
			elseif (not table.contains(target.afflictions, "metrazol")) then
				daeggerhunttoxin = "metrazol"
				send("daegger hunt " .. target.name .. " metrazol")
			elseif (not table.contains(target.afflictions, "noctec")) then
				daeggerhunttoxin = "noctec"
				send("daegger hunt " .. target.name .. " noctec")
			elseif (not table.contains(target.afflictions, "mebaral")) then
				daeggerhunttoxin = "mebaral"
				send("daegger hunt " .. target.name .. " mebaral")
			elseif (not table.contains(target.afflictions, "crippled left arm") and not table.contains(target.afflictions, "crippled right arm")) then
				daeggerhunttoxin = "benzene"
				send("daegger hunt " .. target.name .. " benzene")
			elseif (not table.contains(target.afflictions, "crippled left leg") and not table.contains(target.afflictions, "crippled right leg")) then
				daeggerhunttoxin = "benzedrine"
				send("daegger hunt " .. target.name .. " benzedrine")
			end
		send("order loyals kill " .. target.name .. "::contemplate " .. target.name .. "::deadeyes " .. target.name .. " breach " .. toxins[1])
	else
		cecho("<red>Error - Unknown Dstab Combination")
	end
end

function pickLockToxin()
	for cure, locktoxin in ipairs (Toxin.Lock.queue) do
		if (locktoxin == "bromine") then
			if (not table.contains(target.afflictions, "bromine") and not table.contains(target.mentalaffs, "anorexia")) then
				table.insert(toxins, "bromine")
			end
		else
			if locktoxin == "ether" and not table.contains(target.afflictions, "clumsiness") then
				table.insert(toxins, "ether")
			elseif locktoxin == "arsenic" and not table.contains(target.afflictions, "weariness") then
				table.insert(toxins, "arsenic")
			elseif locktoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif locktoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif locktoxin == "strychnine" and not table.contains(target.afflictions, "sensitivity") then
				table.insert(toxins, "strychnine")
			elseif locktoxin == "xeroderma" and not table.contains(target.afflictions, "sunallergy") then
				table.insert(toxins, "xeroderma")
			elseif locktoxin == "butolinum" and not table.contains(target.afflictions, "nausea") then
				table.insert(toxins, "nausea")
			elseif locktoxin == "mercury" and not table.contains(target.afflictions, "asthma") then
				table.insert(toxins, "mercury")
			elseif locktoxin == "iodine" and not table.contains(target.afflictions, "slickness") then
				table.insert(toxins, "iodine")
			elseif locktoxin == "bromine" and not table.contains(target.mentalaffs, "anorexia") then
				table.insert(toxins, "bromine")
			elseif locktoxin == "mazanor" and not table.contains(target.afflictions, "slow herbs") then
				table.insert(toxins, "mazanor")
			elseif locktoxin == "aconite" and not table.contains(target.mentalaffs, "stupidity") then
				table.insert(toxins, "aconite")
			elseif locktoxin == "atropine" and not table.contains(target.mentalaffs, "recklessness") then
				table.insert(toxins, "atropine")
			elseif locktoxin == "vitriol" and not table.contains(target.mentalaffs, "disloyalty") then
				table.insert(toxins, "vitriol")
			elseif locktoxin == "chloroform" and not table.contains(target.mentalaffs, "epilepsy") then
				table.insert(toxins, "chloroform")
			elseif locktoxin == "noctec" and not table.contains(target.afflictions, "slow balance") then
				table.insert(toxins, "noctec")
			elseif locktoxin == "mebaral" and not table.contains(target.afflictions, "slow equilibrium") then
				table.insert(toxins, "mebaral")
			elseif locktoxin == "hemotoxin" and not table.contains(target.afflictions, "hemotoxin") then
				table.insert(toxins, "hemotoxin")
			elseif locktoxin == "butisol" and not table.contains(target.afflictions, "butisol") then
				table.insert(toxins, "butisol")
			elseif locktoxin == "calotropis" and not table.contains(target.afflictions, "calotropis") then
				table.insert(toxins, "calotropis")
			end
		end
	end
end

function pickKelpToxin()
	for kelp, ktoxin in ipairs (Toxin.Kelp.queue) do
			if ktoxin == "ether" and not table.contains(target.afflictions, "clumsiness") then
				table.insert(toxins, "ether")
			elseif ktoxin == "arsenic" and not table.contains(target.afflictions, "weariness") then
				table.insert(toxins, "arsenic")
			elseif ktoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif ktoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif ktoxin == "strychnine" and not table.contains(target.afflictions, "sensitivity") then
				table.insert(toxins, "strychnine")
			elseif ktoxin == "xeroderma" and not table.contains(target.afflictions, "sunallergy") then
				table.insert(toxins, "xeroderma")
			elseif ktoxin == "butolinum" and not table.contains(target.afflictions, "nausea") then
				table.insert(toxins, "nausea")
			elseif ktoxin == "mercury" and not table.contains(target.afflictions, "asthma") then
				table.insert(toxins, "mercury")
			elseif ktoxin == "iodine" and not table.contains(target.afflictions, "slickness") then
				table.insert(toxins, "iodine")
			elseif ktoxin == "bromine" and not table.contains(target.mentalaffs, "anorexia") then
				table.insert(toxins, "bromine")
			elseif ktoxin == "mazanor" and not table.contains(target.afflictions, "slow herbs") then
				table.insert(toxins, "mazanor")
			elseif ktoxin == "aconite" and not table.contains(target.mentalaffs, "stupidity") then
				table.insert(toxins, "aconite")
			elseif ktoxin == "vitriol" and not table.contains(target.mentalaffs, "disloyalty") then
				table.insert(toxins, "vitriol")
			elseif ktoxin == "chloroform" and not table.contains(target.mentalaffs, "epilepsy") then
				table.insert(toxins, "chloroform")
			elseif ktoxin == "noctec" and not table.contains(target.afflictions, "slow balance") then
				table.insert(toxins, "noctec")
			elseif ktoxin == "mebaral" and not table.contains(target.afflictions, "slow equilibrium") then
				table.insert(toxins, "mebaral")
			elseif ktoxin == "hemotoxin" and not table.contains(target.afflictions, "hemotoxin") then
				table.insert(toxins, "hemotoxin")
			elseif ktoxin == "butisol" and not table.contains(target.afflictions, "butisol") then
				table.insert(toxins, "butisol")
			elseif ktoxin == "calotropis" and not table.contains(target.afflictions, "calotropis") then
				table.insert(toxins, "calotropis")
			end
		if (table.contains(target.afflictions, ktoxin)) then
			kelp_affs = kelp_affs + 1
		end
	end
end

function pickMaidenhairToxin()
	for maidenhair, mtoxin in ipairs (Toxin.Maidenhair.queue) do
			if mtoxin == "ciguatoxin" and (not table.contains(target.afflictions, "numbness") and not table.contains(target.afflictions, "paralyse")) then
				table.insert(toxins, "ciguatoxin")
			elseif mtoxin == "ether" and not table.contains(target.afflictions, "clumsiness") then
				table.insert(toxins, "ether")
			elseif mtoxin == "arsenic" and not table.contains(target.afflictions, "weariness") then
				table.insert(toxins, "arsenic")
			elseif mtoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif mtoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif mtoxin == "strychnine" and not table.contains(target.afflictions, "sensitivity") and not table.contains(target.afflictions, "numbness") then
				table.insert(toxins, "strychnine")
			elseif mtoxin == "metrazol" and not table.contains(target.afflictions, "metrazol") and not table.contains(target.afflictions, "numbness") and target.tree == true then
				table.insert(toxins, "metrazol")
			elseif mtoxin == "xeroderma" and not table.contains(target.afflictions, "sunallergy") then
				table.insert(toxins, "xeroderma")
			elseif mtoxin == "butolinum" and not table.contains(target.afflictions, "nausea") then
				table.insert(toxins, "nausea")
			elseif mtoxin == "mercury" and not table.contains(target.afflictions, "asthma") then
				table.insert(toxins, "mercury")
			elseif mtoxin == "iodine" and not table.contains(target.afflictions, "slickness") then
				table.insert(toxins, "iodine")
			elseif mtoxin == "bromine" and not table.contains(target.mentalaffs, "anorexia") then
				table.insert(toxins, "bromine")
			elseif mtoxin == "mazanor" and not table.contains(target.afflictions, "slow herbs") then
				table.insert(toxins, "mazanor")
			elseif mtoxin == "aconite" and not table.contains(target.mentalaffs, "stupidity") then
				table.insert(toxins, "aconite")
			elseif mtoxin == "vitriol" and not table.contains(target.mentalaffs, "disloyalty") then
				table.insert(toxins, "vitriol")
			elseif mtoxin == "chloroform" and not table.contains(target.mentalaffs, "epilepsy") then
				table.insert(toxins, "chloroform")
			elseif mtoxin == "noctec" and not table.contains(target.afflictions, "slow balance") then
				table.insert(toxins, "noctec")
			elseif mtoxin == "mebaral" and not table.contains(target.afflictions, "slow equilibrium") then
				table.insert(toxins, "mebaral")
			elseif mtoxin == "hemotoxin" and not table.contains(target.afflictions, "hemotoxin") then
				table.insert(toxins, "hemotoxin")
			elseif mtoxin == "butisol" and not table.contains(target.afflictions, "butisol") then
				table.insert(toxins, "butisol")
			elseif mtoxin == "calotropis" and not table.contains(target.afflictions, "calotropis") then
				table.insert(toxins, "calotropis")
			end
			if (table.contains(target.afflictions, mtoxin)) then
				m_affs = m_affs + 1
			end
		end
	end

function pickAllToxin()
	for any, alltoxin in ipairs (Toxin.All.queue) do
		if (alltoxin == "opium") then
			table.insert(toxins, "opium")
			table.insert(toxins, "opium")
		elseif (alltoxin == "benzene2") and not table.contains(target.afflictions, "crippled right arm") then
			table.insert(toxins, "benzene")
		elseif (alltoxin == "benzedrine2") and not table.contains(target.afflictions, "crippled right leg") then
			table.insert(toxins, "benzedrine")
			elseif alltoxin == "ether" and not table.contains(target.afflictions, "clumsiness") then
				table.insert(toxins, "ether")
			elseif alltoxin == "arsenic" and not table.contains(target.afflictions, "weariness") then
				table.insert(toxins, "arsenic")
			elseif alltoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif alltoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif alltoxin == "strychnine" and not table.contains(target.afflictions, "sensitivity") then
				table.insert(toxins, "strychnine")
			elseif alltoxin == "xeroderma" and not table.contains(target.afflictions, "sunallergy") then
				table.insert(toxins, "xeroderma")
			elseif alltoxin == "butolinum" and not table.contains(target.afflictions, "nausea") then
				table.insert(toxins, "nausea")
			elseif alltoxin == "mercury" and not table.contains(target.afflictions, "asthma") then
				table.insert(toxins, "mercury")
			elseif alltoxin == "iodine" and not table.contains(target.afflictions, "slickness") then
				table.insert(toxins, "iodine")
			elseif alltoxin == "bromine" and not table.contains(target.mentalaffs, "anorexia") then
				table.insert(toxins, "bromine")
			elseif alltoxin == "mazanor" and not table.contains(target.afflictions, "slow herbs") then
				table.insert(toxins, "mazanor")
			elseif alltoxin == "aconite" and not table.contains(target.mentalaffs, "stupidity") then
				table.insert(toxins, "aconite")
			elseif alltoxin == "vitriol" and not table.contains(target.mentalaffs, "disloyalty") then
				table.insert(toxins, "vitriol")
			elseif alltoxin == "chloroform" and not table.contains(target.mentalaffs, "epilepsy") then
				table.insert(toxins, "chloroform")
			elseif alltoxin == "noctec" and not table.contains(target.afflictions, "slow balance") then
				table.insert(toxins, "noctec")
			elseif alltoxin == "mebaral" and not table.contains(target.afflictions, "slow equilibrium") then
				table.insert(toxins, "mebaral")
			elseif alltoxin == "hemotoxin" and not table.contains(target.afflictions, "hemotoxin") then
				table.insert(toxins, "hemotoxin")
			elseif alltoxin == "butisol" and not table.contains(target.afflictions, "butisol") then
				table.insert(toxins, "butisol")
			elseif alltoxin == "calotropis" and not table.contains(target.afflictions, "calotropis") then
				table.insert(toxins, "calotropis")
		end
	end
end

--function pickSalveToxin()
--	for any, salvetoxin in ipairs (Toxin.Salve.queue) do
--		if (salvetoxin == "ciguatoxin") then
--			if (not table.contains(target.afflictions, "ciguatoxin") and not target.paralysis) then
--				table.insert(toxins, "ciguatoxin")
--			end
--		else
--			if (salvetoxin == "benzene2") and not table.contains(target.afflictions, "crippled right arm") then
--				table.insert(toxins, "benzene")
--			elseif (salvetoxin == "benzedrine2") and not table.contains(target.afflictions, "crippled right leg") then
--				table.insert(toxins, "benzedrine")
--			else
--				if (salvetoxin == "opium") then
--					table.insert(toxins, "opium")
--					table.insert(toxins, "opium")
--			else
--				if (not table.contains(target.afflictions, salvetoxin)) and ((not salvetoxin == "benzene2") or (not salvetoxin == "benzedrine2")) then
--					table.insert(toxins, salvetoxin)
--					end
--				end
--			end
--		end
--	end
--end

function pickOxalisToxin()
	for any, oxalistoxin in ipairs (Toxin.Oxalis.queue) do
		if (oxalistoxin == "ciguatoxin") then
			if not table.contains(target.afflictions, "paralysis") and not target.paralysis then
				table.insert(toxins, "ciguatoxin")
			end
		else
			if (oxalistoxin == "opium") then
				table.insert(toxins, "opium")
				table.insert(toxins, "opium")
			elseif (oxalistoxin == "benzene2") and not table.contains(target.afflictions, "crippled right arm") then
				table.insert(toxins, "benzene")
			elseif (oxalistoxin == "benzedrine2") and not table.contains(target.afflictions, "crippled right leg") then
				table.insert(toxins, "benzedrine")
			elseif oxalistoxin == "ether" and not table.contains(target.afflictions, "clumsiness") then
				table.insert(toxins, "ether")
			elseif oxalistoxin == "arsenic" and not table.contains(target.afflictions, "weariness") then
				table.insert(toxins, "arsenic")
			elseif oxalistoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif oxalistoxin == "oxalis" and not table.contains(target.afflictions, "deaf") then
				table.insert(toxins, "oxalis")
			elseif oxalistoxin == "strychnine" and not table.contains(target.afflictions, "sensitivity") then
				table.insert(toxins, "strychnine")
			elseif oxalistoxin == "xeroderma" and not table.contains(target.afflictions, "sunallergy") then
				table.insert(toxins, "xeroderma")
			elseif oxalistoxin == "butolinum" and not table.contains(target.afflictions, "nausea") then
				table.insert(toxins, "nausea")
			elseif oxalistoxin == "mercury" and not table.contains(target.afflictions, "asthma") then
				table.insert(toxins, "mercury")
			elseif oxalistoxin == "iodine" and not table.contains(target.afflictions, "slickness") then
				table.insert(toxins, "iodine")
			elseif oxalistoxin == "bromine" and not table.contains(target.mentalaffs, "anorexia") then
				table.insert(toxins, "bromine")
			elseif oxalistoxin == "mazanor" and not table.contains(target.afflictions, "slow herbs") then
				table.insert(toxins, "mazanor")
			elseif oxalistoxin == "aconite" and not table.contains(target.mentalaffs, "stupidity") then
				table.insert(toxins, "aconite")
			elseif oxalistoxin == "vitriol" and not table.contains(target.mentalaffs, "disloyalty") then
				table.insert(toxins, "vitriol")
			elseif oxalistoxin == "chloroform" and not table.contains(target.mentalaffs, "epilepsy") then
				table.insert(toxins, "chloroform")
			elseif oxalistoxin == "noctec" and not table.contains(target.afflictions, "slow balance") then
				table.insert(toxins, "noctec")
			elseif oxalistoxin == "mebaral" and not table.contains(target.afflictions, "slow equilibrium") then
				table.insert(toxins, "mebaral")
			elseif oxalistoxin == "hemotoxin" and not table.contains(target.afflictions, "hemotoxin") then
				table.insert(toxins, "hemotoxin")
			elseif oxalistoxin == "butisol" and not table.contains(target.afflictions, "butisol") then
				table.insert(toxins, "butisol")
			elseif oxalistoxin == "calotropis" and not table.contains(target.afflictions, "calotropis") then
				table.insert(toxins, "calotropis")
			end
		end
	end
end

function pickShadowPlant()
	Shadowplant.plant = "kelp"
end
  ```
  
</details>

<details>
  <summary>Defiler Function Collection (1-8)</summary>
  
  See included files.
  
  ```javascript
  <?xml version="1.0" encoding="ISO-8859-1" ?>
<cmud>
  <class name="Defiler">
    <alias name="ab1">
      <value>ab torment %if(%1,%1,full)</value>
    </alias>
    <alias name="ab2">
      <value>ab shadowbinding %if(%1,%1,full)</value>
    </alias>
    <alias name="ab3">
      <value>ab desecration %if(%1,%1,full)</value>
    </alias>
    <macro key="F5">
      <value>eb touch shield
bash=0
autobash=0
bashing.auto=0
#T- "Autobash Toggle"</value>
    </macro>
    <class name="Torment">
      <alias name="ravage">
        <value>$s=0
#if (@shields() || @rebound_soon()) {$s=1}
@wield(flail)%if(@to_target,{target @to_target@separator})%if($s,devastate,ravage) @tar %-1</value>
      </alias>
      <alias name="ts">
        <value>eb torment @weapons.flail with thornsprout</value>
      </alias>
      <var name="hound">162194</var>
      <trigger name="dash" type="Command Input" priority="71280" trigontrig="false" regex="true">
        <pattern>^d(.+)$</pattern>
        <value>#if %regex(%1,"^(n|ne|e|se|s|sw|w|nw|u|d|i|o)$") {
  %if(@defences.mount,{dismount@separator})mount @hound@{separator}order loyals follow me@{separator}dash %1@{separator}dismount@{separator}mount @mount.mount
  direction=@opp(%1)
  defences.block=0
}</value>
      </trigger>
      <trigger name="hound+" priority="203100" regex="true">
        <pattern>^Chanting quietly, you quickly sketch a pentagram on the ground\. Flames leap high as a hideous hound appears in its center, levelling its gaze upon \a+\.</pattern>
        <value>eb order @hound follow me</value>
      </trigger>
      <alias name="sd">
        <value>eb torment @tar(%1) with shadow</value>
      </alias>
      <alias name="htr">
        <value>@mount(@hound)hound track</value>
      </alias>
      <alias name="bs">
        <value>eb bileshroud %if(%1,off,on)</value>
      </alias>
      <alias name="hd">
        <value>eb torment @tar(%1) with hound</value>
      </alias>
      <alias name="sa">
        <value>eb torment @tar(%1) with arachnids</value>
      </alias>
      <alias name="ww">
        <value>eb torment @tar(%1) with gutwyrm</value>
      </alias>
      <alias name="j">
        <value>#if @tarshield {@wield(flail)devastate @tar mebaral with thornroot@{separator}devastate @tar mebaral} {@sus(behead)@wield(flail)queue eqbal behead @tar}</value>
      </alias>
      <class name="Thornroot">
        <trigger name="thornroot tick" priority="123" regex="true">
          <pattern>^The thornroot burrows deeper into @tar.*'s body, cutting veins and causing considerable bleeding\.</pattern>
          <value>#if @tar {
  thornroot.ignore=0
  thornroot.on=1
  thornroot.tick=@thornroot.tick+1
  #sub {%line [%ansi(15)@thornroot.tick%ansi(0)]}
}</value>
        </trigger>
        <var name="thornroot" type="Record" usedef="true">
          <value>on=0|tick=0|timer=0</value>
          <json>{"on":0,"timer":0,"tick":0}</json>
          <default>on=0|tick=0</default>
        </var>
        <trigger name="root -&gt; seed" priority="123" regex="true">
          <pattern>^Soaked with \a+ blood, the thornroot in @tar.*'s body withers away and disappears, leaving behind an implanted seed\.</pattern>
          <value>#if @tar {
  thornroot.on=0
  thornroot.tick=0
  thornroot.timer=0
  #unt root_timer
  seeds=@seeds+1
  #say %ansi(7)Seeds present: %ansi(15)@seeds
}</value>
        </trigger>
        <var name="seeds" usedef="true">
          <value>0</value>
          <default>0</default>
        </var>
        <trigger name="seed-" priority="123" regex="true">
          <pattern>^A mixture of pain and relief crosses the face of @tar.* as the seed within \a+ finally perishes and withers away, burning \a+ insides\.</pattern>
          <value>#if @tar {
  seeds=@seeds-1
  #if @seeds&lt;1 {seeds=0}
  #say %ansi(7)Seeds left: %ansi(15)@seeds
  #if !@thornroot.on {
    thornroot.tick=0
    thornroot.timer=0
    #unt root_timer
  }
}</value>
        </trigger>
        <trigger name="root explode" priority="205890" regex="true">
          <pattern>^Horror overcomes @tar.*'s tormented face as the thornroot in \a+ body suddenly explodes in a violent outburst of blood and gore\.</pattern>
          <value>#if @tar {
  #if !@thornroot.ignore {
    thornroot.on=0
    thornroot.tick=0
    thornroot.timer=0
    #unt root_timer
  }
  thornroot.ignore=0
  #say @echo(13,14," Root Exploded!! ")
}</value>
        </trigger>
        <trigger name="seed+" priority="205910" regex="true">
          <pattern>^A demonic seed is left behind in @tar.*'s body\.$</pattern>
          <value>#if @tar {
  thornroot.on=0
  thornroot.tick=0
  thornroot.timer=0
  #unt root_timer
  seeds=@seeds+1
  #say %ansi(7)Seeds present: %ansi(15)@seeds
}</value>
        </trigger>
        <trigger name="bellow" priority="123" regex="true">
          <pattern>^At .* order, .* bellows loudly at @tar</pattern>
          <value>seeds=0</value>
        </trigger>
        <trigger name="thorns-" priority="213030" regex="true">
          <pattern>^There are no more thorns left on the flail\.</pattern>
          <value>#say @echo(13,14," THORNSPROUT! ")</value>
        </trigger>
        <trigger name="root-" priority="214510" regex="true">
          <pattern>^A look of pain crosses @tar.*'s tormented face\.</pattern>
          <value>#if @tar {
  thornroot.on=0
  thornroot.tick=0
  thornroot.timer=0
  #unt root_timer
}</value>
        </trigger>
        <trigger name="evolve" priority="217910" regex="true">
          <pattern>^At your command, .* envelops @tar.* briefly with its tendrils and @tar.* shudders briefly\. .*</pattern>
          <value>#if @tar {
  seeds=1
  thornroot.on=0
  thornroot.tick=0
  thornroot.timer=0
  #unt root_timer
}</value>
        </trigger>
        <trigger name="rebuke" priority="232550" regex="true">
          <pattern>^You parry the attack and deftly fire a thornroot at @tar.*, which quickly burrows into \a+ flesh\.</pattern>
          <value>#if @tar {
  thornroot.on=1
  thornroot.timer=0
  thornroot.tick=0
  #alarm root_timer *1 {
    thornroot.timer=@thornroot.timer+1
    #if @thornroot.timer&gt;25 {
      thornroot.timer=0
      thornroot.on=0
      #unt root_timer
    }
  }
}</value>
        </trigger>
        <trigger name="thornfire" priority="123" regex="true">
          <pattern>^You aim with your flail and fire a thornroot towards (\a+)\. It rapidly reaches \a+ and starts burrowing.*</pattern>
          <value>#if @tar {
  thornroot.on=1
  thornroot.tick=0
  thornroot.timer=0
  #alarm root_timer *1 {
    thornroot.timer=@thornroot.timer+1
    #if @thornroot.timer&gt;25 {
      thornroot.timer=0
      thornroot.on=0
      #unt root_timer
    }
  }
}</value>
        </trigger>
        <trigger name="with thornroot" priority="292790" regex="true">
          <pattern>^A thornroot splits from the flail and starts burrowing into @tar.*'s flesh\.</pattern>
          <value>#if @tar {
  thornroot.on=1
  thornroot.tick=0
  thornroot.timer=0
  thornroot.ignore=1
  #alarm root_timer *1 {
    thornroot.timer=@thornroot.timer+1
    #if @thornroot.timer&gt;25 {
      thornroot.timer=0
      thornroot.on=0
      #unt root_timer
    }
  }
}</value>
        </trigger>
        <trigger name="root- " priority="295800" regex="true">
          <pattern>^Soaked with \a+ blood, the thornroot in @tar.*'s body withers away and disappears\.</pattern>
          <value>#if @tar {
  thornroot.on=0
  thornroot.tick=0
  thornroot.timer=0
  #unt root_timer
}</value>
        </trigger>
        <trigger name="implant" priority="123" regex="true">
          <pattern>^[a-z, \-]+ emits a bloodcurdling hollow howl as its tendrils enter @tar.*'s mouth in a sinuous movement and inject a \a+ demonic seed into h.. throat\. The \a+ seeds enroot themselves deep within \a+'s flesh\.</pattern>
          <value>seeds=@seeds+1</value>
        </trigger>
      </class>
      <trigger name="track+" priority="219950" regex="true">
        <pattern>^Your hound growls in satisfaction as the black portal grows in size and moves directly into its depths\. A moment later, you find yourself elsewhere, the portal collapsing behind you\.</pattern>
        <value>@mount(@mount.mount)</value>
      </trigger>
      <class name="Unseeneye">
        <alias name="un">
          <value>#if %1 {eb torment %1 with the unseen eye} {unseeneye list}</value>
        </alias>
        <alias name="ul">
          <value>#if %1 {eb unseeneye %1 listen %if(%2,off,on)} {unseeneye list}</value>
        </alias>
        <alias name="ug">
          <value>#if %1 {eb unseeneye %1 look} {unseeneye list}</value>
        </alias>
        <var name="unseeneye" type="StringList" sorted="1">
          <value>|Eldreth|Iluv|Kitt|Maglust|Sadey|Sumie|Tristam|Zcephonie</value>
          <json>["","Eldreth","Iluv","Kitt","Maglust","Sadey","Sumie","Tristam","Zcephonie"]</json>
        </var>
        <trigger name="unseeneye list" priority="234160" regex="true">
          <pattern>^You have tormented the following individuals with the unseen eye\:</pattern>
          <value>unseeneye=%null
#sub {Unseen eye list:}</value>
          <trigger param="100" repeat="true" regex="true" prompt="true">
            <pattern>.*</pattern>
            <value>#if %regex(%line,@prompt) {#state 0} {
  unseeneye=%subregex(%line,", |\.",|)
  #state 0
}
#gag
#sort unseeneye
#forall @unseeneye {
  #say %ansi(0)%i
}</value>
          </trigger>
        </trigger>
      </class>
      <var name="shroud">bile</var>
      <trigger name="___shroud" priority="123" regex="true">
        <pattern>^You focus upon your demonic powers and concentrate, and a swirling (\a+)shroud slowly forms around your body, protecting you from harm\.</pattern>
        <value>shroud=%1</value>
      </trigger>
      <trigger name="___shroud-" priority="123" regex="true">
        <pattern>^You dismiss the swirling \a+shroud and it quickly dissipates\.</pattern>
        <value>shroud=""</value>
      </trigger>
      <trigger name="___shroud explode" priority="123" regex="true">
        <pattern>^You concentrate and the shroud of \a+ around you suddenly explodes outwards\.</pattern>
        <value>shroud=""</value>
      </trigger>
      <alias name="sh">
        <value>queue eqbal %switch(%1=f,flame,%1=i,ice,bile)shroud on</value>
      </alias>
      <alias name="xx">
        <value>queue eqbal @{shroud}shroud explode</value>
      </alias>
      <trigger name="___shroud up" priority="123" regex="true">
        <pattern>^Your (\a+)shroud is already on\.</pattern>
        <value>shroud=%1</value>
      </trigger>
    </class>
    <class name="Desecration">
      <var name="treant">156528</var>
      <alias name="te">
        <value>#local $a
#if @splintered.hawthorn {
  $a={treant empower @tar hawthorn}
} {
  #if @whirl {
    $a={whirl branch at @tar hawthorn\\treant empower @tar hawthorn}
  }
}
%if($a,{$a\\})queue eqbal treant entangle @tar(%1)</value>
      </alias>
      <alias name="hs">
        <value>eb treant sustain</value>
      </alias>
      <trigger name="implant-" priority="209780" regex="true">
        <pattern>^Your target must be hindered in some way for that to be possible\.</pattern>
        <value>#sendraw {@sus()}</value>
      </trigger>
      <alias name="ti">
        <value>@sus(implant)queue eqbal treant implant @tar(%1)</value>
      </alias>
      <trigger name="implant- " priority="210140" regex="true">
        <pattern>^@tar.* has writhed free from the tendrils of the treant\.</pattern>
        <value>#if @beheading=implant {
  #sendraw {@sus()}
}</value>
      </trigger>
      <alias name="fu">
        <value>eb treant woodheart</value>
      </alias>
      <alias name="ev">
        <value>eb treant evolve @tar(%1)</value>
      </alias>
      <trigger name="treant attack+" priority="224370" regex="true">
        <pattern>^You order .*treant.* to attack (\a+)\.\n[a-z ]+ obeys your command\.</pattern>
        <value>treant_attacking=%1
#if %1!=yourself {tar=%1}</value>
      </trigger>
      <var name="treant_attacking" usedef="true"/>
      <trigger name="treant attack-" priority="224390" regex="true">
        <pattern>^You order .*treant.* to assume a passive stance\.\nWith seeming reluctance, .*treant.* obeys your command\.</pattern>
        <value>treant_attacking=""</value>
      </trigger>
      <class name="Blood">
        <alias name="bv">
          <value>eb spatter blackvines from @blood(5)</value>
        </alias>
        <alias name="wpl">
          <value>eb spatter whirlpool from @blood(5)</value>
        </alias>
        <alias name="temp">
          <value>eb spatter tempest from @blood(20)</value>
        </alias>
        <alias name="rain">
          <value>eb spatter bloodrain from @blood(10) %1</value>
        </alias>
        <alias name="hbl">
          <value>eb spatter heartblood from @blood(50) %if(%1,%1,ciguatoxin)</value>
        </alias>
        <trigger name="blood" priority="235910" regex="true">
          <pattern>^vial(\d+)\s+tainted blood\s+(\d+)</pattern>
          <value>#addk blood %1 %2</value>
        </trigger>
        <var name="blood" type="Record">
          <value>249942=50|249709=50|14961=50|11793=50|235645=50|234792=50|226643=50|224328=50|154609=50|141261=50|133269=50|107003=50|105524=50|68028=50|9774=50|7621=50|6267=50|31458=50</value>
          <json>{"249942":50,"249709":50,"14961":50,"11793":50,"235645":50,"234792":50,"226643":50,"224328":50,"154609":50,"141261":50,"133269":50,"107003":50,"105524":50,"68028":50,"9774":50,"7621":50,"6267":50,"31458":50}</json>
        </var>
        <func name="blood">
          <value>$u=%1
#loopdb @blood {
  #if @blood.%key&gt;=$u {
    #return {%key}
    #break
  }
}</value>
        </func>
        <alias name="blood">
          <value>blood=%null
elixlist blood</value>
        </alias>
        <alias name="reblood">
          <value>empty %1@{separator}leech blood into %1</value>
        </alias>
      </class>
      <trigger name="hearken" priority="236120" regex="true">
        <pattern>^Your treant notifies you that (\a+) has (\a+) the room\.</pattern>
        <value>;#if (@broadcast &amp;&amp; !@isally(%1)) {#additem movement %1_%2}
#sub {%ansi(8)Hearken - %ansi(7)%proper(%2)%ansi(7): %ansi(15)%1}</value>
      </trigger>
      <class name="Splinter">
        <var name="splintered" type="Record" usedef="true">
          <value>beech=0|birch=0|blackthorn=0|elder=0|hazel=0|honeysuckle=0|hawthorn=0|willow=0</value>
          <json>{"blackthorn":0,"hawthorn":0,"honeysuckle":0,"hazel":0,"elder":0,"willow":0,"birch":0,"beech":0}</json>
          <default>beech=0|birch=0|blackthorn=0|elder=0|hazel=0|honeysuckle=0|hawthorn=0|willow=0</default>
        </var>
        <trigger name="splinter" priority="123" regex="true">
          <pattern>^At .* command, .* swings a branch rapidly at @tar.*\. Upon impact, the branch splinters, releasing a small (\a+) seed that burrows into \a+ skin and drains a portion of the energy stored within the burrowing thornroot\.</pattern>
          <value>#if @tar {
  $s=%1
  splintered.$s=30
  #alarm splinter_$s *1 {
    #addk splintered $s (@{splintered.$s}-1)
    #if @splintered.$s&lt;1 {
      #addk splintered $s 1
      #unt splinter_$s
    }
  }
}</value>
        </trigger>
        <trigger name="splinter-" priority="500000" regex="true">
          <pattern>^The splintered (\a+) seed in @tar.*'s body has withered away\.</pattern>
          <value>#unt splinter_%1
splintered.%1=0</value>
        </trigger>
        <alias name="sp">
          <value>$a={treant shred @tar}
#if @afflicted.addiction {$a={treant intoxicate @tar}}
#if @afflicted.dryblood {$a={torment @tar with haemophilia}}
#if @afflicted.haemophilia {$a={treant rigidity @tar}}
#if @afflicted.rigidity {$a={torment @tar with hallucinations}}
#if @afflicted.hallucinations {$a={treant shred @tar}}
#local $s $w
#if %1 {
  #switch %1
    (b) {$s=birch}
    (l) {$s=blackthorn}
    (e) {$s=elder}
    (h) {$s=hawthorn}
    (a) {$s=ash}
    (w) {$s=willow}
    (y) {$s=honeysuckle}
    (e) {$s=beech}
}
#if !$s {
  #forall {birch|blackthorn|elder|hawthorn|honeysuckle|beech|ash|willow} {
    #if !@splintered.%i {
      $s=%i
      #break
    }
  }
}
$t=@pick(t,defilerQ)
#local $r
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
%if(@to_target,{target @to_target@{separator}})@wield(flail)%if(!@treant_attacking,%exec(fk)@separator)$r @tar $t %if(!@thornroot.on,{ with thornroot@{separator}$r @tar $t})@{separator}treant splinter @tar $s\\$a</value>
        </alias>
        <trigger name="empower" priority="123" regex="true">
          <pattern>^At .* command, .* cuts @tar.* lightly, releasing a small shockwave that causes the (\a+) seed in \a+ body to start stirring\.</pattern>
          <value>#unt splinter_%1
splintered.%1=0</value>
        </trigger>
        <trigger name="germinate" priority="123" regex="true">
          <pattern>^Following .* command, .* releases a strangely smelling substance that seems into @tar.*'s pores\. The (\a+) seed inside \a+ suddenly erupts into life before withering away\.</pattern>
          <value>#unt splinter_%1
splintered.%1=0
#if %1=willow {relapsing.ten=check}
#if %1=ash {relapsing.five=check}</value>
        </trigger>
        <class name="Relapse Checks">
          <var name="relapsing" type="Record" usedef="true">
            <value>five|ten</value>
            <json>{"ten":"","five":""}</json>
            <default>five=""|ten=""</default>
          </var>
          <trigger name="haemophilia" priority="123" regex="true">
            <pattern>^You close your eyes and focus on thinning @tar.*'s blood. For a moment nothing happens, then an uncomfortable look crosses the face of @tar.* and s?he is briefly surrounded by a red nimbus\.</pattern>
            <value>#if @relapsing.ten=check {relapsing.ten=haemophilia} {
  #if @relapsing.five=check {relapsing.five=haemophilia}
}</value>
          </trigger>
          <trigger name="hallucinations" priority="123" regex="true">
            <pattern>^You whisper words of madness into @tar.*'s ears and watch with satisfaction as h\a+ eyes open wide upon seeing something that isn't there\.</pattern>
            <value>#if @relapsing.ten=check {relapsing.ten=hallucinations} {
  #if @relapsing.five=check {relapsing.five=hallucinations}
}</value>
          </trigger>
        </class>
        <alias name="spw">
          <value>$a={treant shred @tar}
#if @afflicted.addiction {$a={treant intoxicate @tar}}
#if @afflicted.dryblood {$a={torment @tar with haemophilia}}
#if @afflicted.haemophilia {$a={treant rigidity @tar}}
#if @afflicted.rigidity {$a={torment @tar with hallucinations}}
#if @afflicted.hallucinations {$a={treant shred @tar}}
#local $s $w
#if %1 {
  #switch %1
    (b) {$s=birch}
    (l) {$s=blackthorn}
    (e) {$s=elder}
    (h) {$s=hawthorn}
    (a) {$s=ash}
    (w) {$s=willow}
    (y) {$s=honeysuckle}
    (e) {$s=beech}
}
#if !$s {
  #forall {birch|blackthorn|elder|hawthorn|honeysuckle|beech|ash|willow} {
    #if !@splintered.%i {
      $s=%i
      #break
    }
  }
  #forall {birch|blackthorn|elder|hawthorn|honeysuckle|beech|ash|willow} {
    #if (!@splintered.%i &amp;&amp; $s!=%i) {
      $w=%i
      #break
    }
  }
}
$t=@pick(t,defilerQ)
#local $r
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
%if(@to_target,{target @to_target@{separator}})@wield(flail)%if(!@treant_attacking,%exec(fk)@separator)$r @tar $t %if(!@thornroot.on,{ with thornroot@{separator}$r @tar $t})@{separator}treant splinter @tar $s\\%if($w,{whirl branch at @tar $w\\})$a</value>
        </alias>
        <trigger name="whirl" priority="295630" regex="true">
          <pattern>^You whirl your arm stump mightily towards @tar.*, releasing a spore of the (\a+) tree that swiftly burrows into \a+ skin\.</pattern>
          <value>whirl=0
$s=%1
splintered.$s=30
#alarm splinter_$s *1 {
  #addk splintered $s (@{splintered.$s}-1)
  #if @splintered.$s&lt;1 {
    #addk splintered $s 1
    #unt splinter_$s
  }
}
whirl_timer=15
#alarm whirl_reset *1 {
  whirl_timer=@{whirl_timer}-1
  #if @whirl_timer&lt;1 {
    #unvar whirl_timer
    #unt whirl_reset
  }
}</value>
        </trigger>
        <trigger name="whirl+" priority="295610" regex="true">
          <pattern>^You are again able to whirl your arm stump\.</pattern>
          <value>whirl=1</value>
        </trigger>
        <trigger name="whirl-" priority="295650" regex="true">
          <pattern>^You are not able to whirl your arm stump again so soon\.</pattern>
          <value>whirl=0</value>
        </trigger>
        <trigger name="whirl 3rd" priority="123" regex="true">
          <pattern>^\a+ whirls? \a+ arm stump mightily towards @tar.*, releasing a spore of the (\a+) tree that swiftly burrows into \a+ skin\.</pattern>
          <value>#if @tar {
  $s=%1
  splintered.$s=30
  #alarm splinter_$s *1 {
    #addk splintered $s (@{splintered.$s}-1)
    #if @splintered.$s&lt;1 {
      #addk splintered $s 1
      #unt splinter_$s
    }
  }
}</value>
        </trigger>
        <var name="whirl">1</var>
        <trigger name="germinate 3rd " priority="23" regex="true">
          <pattern>^[a-z, \-]+ starts releasing a strangely smelling substance and cuts @tar.* lightly, causing it to seep into h.. pores\. The (\a+) seed inside h.. suddenly erupts into life before withering away\.</pattern>
          <value>#unt splinter_%1
splintered.%1=0
#if %1=willow {relapsing.ten=check}
#if %1=ash {relapsing.five=check}</value>
        </trigger>
      </class>
      <alias name="branch">
        <value>@wield(flail)treant corrupt left</value>
      </alias>
    </class>
    <macro key="F1">
      <value>@wield(flail)ravage @tar</value>
    </macro>
    <macro key="F8" enabled="false">
      <value>#local $s $b $r $w $sa
#if @splintered.hawthorn {#additem $sa {treant germinate @tar hawthorn}}
#if @splintered.birch {#additem $sa {treant germinate @tar birch}}
#if @splintered.elder {#additem $sa {treant germinate @tar elder}}
#if @seeds {
  #if !@splintered.birch {
    $w={whirl branch at @tar birch\\treant germinate @tar birch}
  }
}
#if (100*@tarhp.cur/@tarhp.max)&lt;30 {$s=1} {
  $s=0
  #if @splintered.blackthorn {
    $b=1
    #if @whirl {
      $w={whirl branch at @tar elder\\treant germinate @tar elder\\}
    }
  } {
    #if @whirl {
      $w={whirl branch at @tar blackthorn\\}
      $b=1
    }
  }
}
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
%if(!@treant_attacking,%exec(fk)@separator)$r" "@tar" "@pick(t,defilerQ)" "%if(!@thornroot.on,"with thornroot")\\%if($sa,{%expandlist($sa,"\\")\\})%if($w,{$w})%if($b,{treant empower @tar blackthorn\\})treant bellow @tar\\treant %if($s,trip,shred) @tar</value>
    </macro>
    <macro key="F2">
      <value>#local $p
$a={treant shred @tar}
#if @afflicted.addiction {$a={treant intoxicate @tar}}
#if @afflicted.dryblood {$a={torment @tar with haemophilia}}
#if @afflicted.haemophilia {$a={treant rigidity @tar}}
#if @afflicted.rigidity {$a={torment @tar with hallucinations}}
#if @afflicted.hallucinations {$a={treant shred @tar}}
$t=@pick(t,defilerQ)
#local $r
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
%if(@to_target,{target @to_target@{separator}})@wield(flail)%if(!@treant_attacking,%exec(fk)@separator)$r @tar $t %if(!@thornroot.on,{ with thornroot@{separator}$r @tar $t})@{separator}$a</value>
    </macro>
    <var name="defilerQ" type="StringList">
      <value>xeroderma|botulinum|ciguatoxin|metrazol|hemotoxin</value>
      <json>["xeroderma","botulinum","ciguatoxin","metrazol","hemotoxin"]</json>
    </var>
    <macro key="ALT-S">
      <value>ravage @pick(t,defilerQ)\\treant trip @tar</value>
    </macro>
    <macro key="F4">
      <value><![CDATA[#local $a $p $r $s
cycle.max=5
#switch @cycle.val
  (1) {
  #if @splintered.birch {$p={treant germinate @tar birch}}
  $a={treant shred @tar}
  #if (!$p && @whirl) {
    $p={whirl branch at @tar birch\\treant germinate @tar birch}
  }
}
  (2) {
  $a={treant intoxicate @tar}
  #if @splintered.elder {$p={treant empower @tar elder}}
  #if (!$p && @whirl) {
    $p={whirl branch at @tar elder\\treant empower @tar elder}
  }
}
  (3) {
  $a={torment @tar with haemophilia}
  #forall {willow|ash} {
    #if @splintered.%i {
      $p={treant germinate @tar %i}
      #break
    }
  }
  #if (!$p && @whirl) {
    $p={whirl branch at @tar ash\\treant germinate @tar ash}
  }
}
  (4) {$a={treant rigidity @tar}}
  (5) {
  $a={torment @tar with hallucinations}
  #forall {willow|ash} {
    #if @splintered.%i {
      $p={treant germinate @tar %i}
      #break
    }
  }
  #if (!$p && @whirl) {
    $p={whirl branch at @tar ash\\treant germinate @tar ash}
  }
}
#if (@whirl && !%regex($p,whirl) && !@splintered.birch) {$p=%concat({whirl branch at @tar birch\\treant germinate @tar birch\\},$p)}
#if @tarshield {$p=""}
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
%if(@to_target,{target @to_target@{separator}})@wield(flail)%if(!@treant_attacking,%exec(fk)@separator)%if($p,$p"\\")$r" "@tar" "@pick(t,defilerQ)" "%if(!@thornroot.on,{ with thornroot@{separator}$r" "@tar" "@pick(t,defilerQ)})@{separator}$a]]></value>
    </macro>
    <macro key="ALT-1">
      <value>ravage @pick(t) %if(!@thornroot.on,"with thornroot")\\%if(!@afflicted.rigidity,{treant rigidity @tar},{treant trip @tar})</value>
    </macro>
    <alias name="rme">
      <value>touch jera on me\\touch algiz on me\\touch berkana on me</value>
    </alias>
    <alias name="fk">
      <value>order @treant kill @tar(%1)</value>
    </alias>
    <alias name="emp">
      <value>eldreth empress me</value>
    </alias>
    <macro key="ALT-2">
      <value>ravage butisol %if(!@thornroot.on,"with thornroot")\\shadowbind @tar with augury of applying restoration to back</value>
    </macro>
    <macro key="ALT-3">
      <value>give fish to @tar\\devastate @tar @pick(t,defilerQ) %if(!@thornroot.on,"with thornroot")\\shadowbind @tar with augury of eating fish</value>
    </macro>
    <alias name="lame">
      <value>give %1 to @tar\\devastate @tar @pick(t,defilerQ) %if(!@thornroot.on,"with thornroot")\\shadowbind @tar with augury of eating %1</value>
    </alias>
    <macro key="ALT-X">
      <value>ravage opium\\shadowbind @tar with sleep</value>
    </macro>
    <class name="Shadowbinding">
      <alias name="anb">
        <value>eb shadowbind me with antibodies %if(%1,off)</value>
      </alias>
      <alias name="apr">
        <value>eb shadowbind here with apparition</value>
      </alias>
      <alias name="bss">
        <value>eb shadowbind @tar(%1) with breathsap</value>
      </alias>
      <alias name="cf">
        <value>eb shadowbind @tar(%1) with confutation</value>
      </alias>
      <alias name="ck">
        <value>eb shadowbind me with shroud</value>
      </alias>
      <alias name="dr">
        <value>eb shadowbind @tar(%1) with healthdrain</value>
      </alias>
      <alias name="fs">
        <value>eb shadowbind @tar(%1) with firesap</value>
      </alias>
      <alias name="ing">
        <value>eb siphon haze into %if(%1,%1,shadowcrown)</value>
      </alias>
      <alias name="jx">
        <value>@sus(juxtaposition)queue eqbal shadowbind here with juxtaposition</value>
      </alias>
      <alias name="md">
        <value>eb shadowbind @tar(%1) with manadrain</value>
      </alias>
      <alias name="obs">
        <value>eb shadowbind here with obscurity</value>
      </alias>
      <alias name="pss">
        <value>eb shadowbind @tar(%1) with poisonsap</value>
      </alias>
      <alias name="pt">
        <value>eb shadowbind here with mistbind</value>
      </alias>
      <alias name="py">
        <value>eb shadowbind @tar(%1) with psysap</value>
      </alias>
      <alias name="sg">
        <value>shadowgems</value>
      </alias>
      <alias name="ssee">
        <value>eb shadowbind seek @tar(%1)</value>
      </alias>
      <alias name="tr">
        <value>eb shadowbind @tar(%1) with transmission</value>
      </alias>
      <alias name="v">
        <value>eb shadowbind me with projection %if(%1,%1,@direction)</value>
      </alias>
      <var name="apparition" type="Literal"/>
      <trigger name="apparition+" priority="237710" regex="true">
        <pattern>^You rub a shadowgem and an apparition materialises slowly in the room, taking on your appearance\.</pattern>
        <value>apparition.room=%gmcp.room.info.name
apparition.vnum=%gmcp.room.info.num
apparition.time=150
#alarm apparition_timer *1 {
  apparition.time=@{apparition.time}-1
  #if @apparition.time&lt;1 {
    apparition.time=0
    #unt apparition_timer
  }
}</value>
      </trigger>
      <trigger name="apparition-" priority="237740" regex="true">
        <pattern>^Your apparition has vanished\.</pattern>
        <value>apparition=""
#unt apparition_timer</value>
      </trigger>
      <trigger name="juxtaposition-" priority="234640" regex="true">
        <pattern>^You cease maintaining the juxtaposition and planes separate themselves once again\.</pattern>
        <value>#sendraw {@sus()}</value>
      </trigger>
      <trigger name="shadowgem" priority="202880" regex="true">
        <pattern>^"shadowveil(\d+)"\s+a constructed shadowveil</pattern>
        <value>get 50 shadowgem from %1</value>
      </trigger>
      <stat name="apparition" priority="237730">
        <value>%if(@apparition,{[ apparition : @apparition.room (@apparition.vnum) - @apparition.time ]})</value>
      </stat>
    </class>
    <alias name="em">
      <value>whirl branch at @tar %1\\treant empower @tar %1</value>
    </alias>
    <alias name="ge">
      <value>whirl branch at @tar %1\\treant germinate @tar %1</value>
    </alias>
    <alias name="rr">
      <value>#local $r
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
$r @tar" "@pick(t,defilerQ) with thornroot</value>
    </alias>
    <trigger name="cycle+" priority="123" regex="true">
      <pattern>^Smiling sadistically, you</pattern>
      <value>cycle.val=@{cycle.val}+1
#if @cycle.val&gt;@cycle.max {cycle.val=1}</value>
    </trigger>
    <macro key="F3">
      <value>#local $s $b $r $w $sa
#if !@tarshield {
  #if @splintered.hawthorn {#additem $sa {treant germinate @tar hawthorn}}
  #if @splintered.birch {#additem $sa {treant germinate @tar birch}}
  #if @splintered.elder {#additem $sa {treant germinate @tar elder}}
}
#if @seeds {
  #if !@tarshield {
    #if !@splintered.birch {
      $w={whirl branch at @tar birch\\treant germinate @tar birch}
    }
  }
}
//#if (100*@tarhp.cur/@tarhp.max)&lt;30 {$s=1} {
  //$s=0
  $s=0
  #if !@tarshield {
    #if @splintered.blackthorn {
      $b=1
      #if @whirl {
        $w={whirl branch at @tar elder\\treant germinate @tar elder}
      }
    } {
      #if @whirl {
        $w={whirl branch at @tar blackthorn\\}
        $b=1
      }
    }
  }
//}
#if (@shields() || @rebound_soon()) {$r=devastate} {$r=ravage}
%if($sa,{%expandlist($sa,"\\")\\})%if($w,{$w\\})%if($b,{treant empower @tar blackthorn\\})%if(!@treant_attacking,%exec(fk)@separator)$r" "@tar" "@pick(t,defilerQ)" "%if(!@thornroot.on,"with thornroot")\\treant bellow @tar\\treant %if($s,trip,shred) @tar</value>
    </macro>
  </class>
</cmud>
  ```
  
</details>

<details>
  <summary>Iniar's Doblinker</summary>
  
  Spoiler text. Note that it's important to have a space after the summary tag. You should be able to write any markdown you want inside the `<details>` tag... just make sure you close `<details>` afterward.
  
  ```lua
  function doblink( room, color, duration )
	local colorn, durationn
	local r,g,b,br,bg,bb
	room = tonumber(room)
	if type(color) == "table" then
		r,g,b,br,bg,bb = unpack(color)
	end
	if not color then
		r,g,b,br,bg,bb = 220,0,0,255,186,23
	end
	if not duration then durationn = 5 else durationn = duration end

	highlightRoom(room,r,g,b,br,bg,bb,1,255,255)
	for i=0.5, durationn do
		tempTimer( i, function() highlightRoom( room, r,g,b,br,bg,bb, 1, 255, 255 ) end )
		tempTimer( i+0.5, function() unHighlightRoom( room ) end )
	end
	tempTimer( durationn + 1, function() unHighlightRoom( room ) end )
end
  ```
  
</details>
