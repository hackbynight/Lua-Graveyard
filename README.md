See above for projects, files, and heads - see below for a ~~collection~~ unorganized dump of LUA scripts.


# A few LUA scripts to share with friends. 




  <summary>Discord Web Hook</summary>
  
  Basic webhook to take messages given/taken via telnet and pump it into my private discord.
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

  local url = "https://discordapp.com/api/webhooks/yourapigoeshere"
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
  -- local url = "https://discordapp.com/api/webhooks/yourapigoeshere"
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


  <summary>Do, Or</summary>
  
  ```lua
  -- Original (2011-2014) Credit to Vadim Peretokin, 2014+ lots of us. Vadi's license below

-- Svof is licensed under a
-- Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License.

-- You should have received a copy of the license along with this
-- work. If not, see <http://creativecommons.org/licenses/by-nc-sa/4.0/>.

-- Svof's DOR system, implemented as a balanceless and a balancefun action

svo = svo or {}; svo.loader = svo.loader or {}
svo.loader.dor = function()

local sk, me, sys = svo.sk, svo.me, svo.sys
local bals = svo.bals

function sk.getactiondo(i)
  local action = me.doqueue[i]
  if not action then return '' end

  if type(action) == 'table' then
    return tostring(action.what)
  else
    return tostring(action)
  end
end

sk.dometatable = {
  __tostring = function (self)
    return self.what
  end
}

function svo.dofirst(what, echoback, show)
  if type(show) == 'nil' then
    table.insert(me.doqueue, 1, what)
  else
    local t = {what = what, show = show}
    setmetatable(t, sk.dometatable)
    table.insert(me.doqueue, 1, t)
  end

  if echoback then svo.echof("Will do \"%s\" first.", tostring(what)) end
  -- spur the queue into doing work right away, unless this came from a trigger - in which case the prompt will make the stuff work anyhow
  if not debug.traceback():find('Trigger', 1, true) then svo.make_gnomes_work() end
  raiseEvent("svo do changed")
end
function svo.dofreefirst(what, echoback)
  table.insert(me.dofreequeue, 1, what)
  if echoback then svo.echof("Will do \"%s\" first in dofree.", tostring(what)) end
  if not debug.traceback():find('Trigger', 1, true) then svo.make_gnomes_work() end
  raiseEvent("svo dofree changed")
end

function svo.doadd(what, echoback, show)
  if type(show) == 'nil' then
    me.doqueue[#me.doqueue+1] = what
  else
    local t = {what = what, show = show}
    setmetatable(t, sk.dometatable)
    me.doqueue[#me.doqueue+1] = t
  end

  if echoback then svo.echof("Added '%s' to the do queue.", tostring(what)) end
  if not debug.traceback():find('Trigger', 1, true) then svo.make_gnomes_work() end
  raiseEvent("svo do changed")
end

function svo.doaddfree(what, echoback)
  me.dofreequeue[#me.dofreequeue+1] = what
  if echoback then svo.echof("Added '%s' to the dofree queue.", tostring(me.dofreequeue[#me.dofreequeue])) end
  if not debug.traceback():find('Trigger', 1, true) then svo.make_gnomes_work() end
  raiseEvent("svo dofree changed")
end

function svo.donext()
  sys.balancetick = sys.balancetick + 1
  if sys.actiontimeoutid then
    killTimer(sys.actiontimeoutid)
    sys.actiontimeoutid = false
  end
  if not debug.traceback():find('Trigger', 1, true) then svo.make_gnomes_work() end
end

function svo.dor (what, echoback, show)
  if not what or what == 'off' then
    if me.doqueue.repeating or what == 'off' then
      me.doqueue = {repeating = false}
      if echoback then svo.echof("Do-Repeat %s.", svo.red('disabled')) end
    else
      me.doqueue.repeating = true
      if echoback and #me.doqueue > 0 then
        svo.echof("Do-Repeat %s; will repeat the first command (%s) in the queue%s.", svo.green('enabled'), sk.getactiondo(1), (me.dopaused and ", but the do queue is currently paused" or ""))
      elseif echoback then
        svo.echof("Do-Repeat %s; will repeat the first command (which is nothing right now) in the queue%s.", svo.green('enabled'), (me.dopaused and ", but the do queue is currently paused" or ""))
      end
    end
  else
    me.doqueue.repeating = true
    if type(show) == 'nil' then
      me.doqueue[1] = what
    else
      me.doqueue[1] = {what = what, show = show}
    end

    if echoback then svo.echof("Do-Repeat %s; will repeat %s forever%s.", svo.green('enabled'), sk.getactiondo(1), (me.dopaused and ", but the do queue is currently paused" or "")) end
  end
  if not debug.traceback():find('Trigger', 1, true) then svo.make_gnomes_work() end
  raiseEvent("svo do changed")
end

function sk.check_do()
  if not bals.balance or not bals.equilibrium or not bals.rightarm or not bals.leftarm or svo.doworking or me.dopaused then return end

  if #me.doqueue == 0 then return end

  svo.doworking = true

  local action = me.doqueue[1]
  local show
  if type(action) == 'table' then
    show = action.show
    action = action.what
  end

  if type(action) == 'string' then
    for _,w in ipairs(string.split(action, "%$")) do
      if type(show) == 'nil' then
        pcall(expandAlias, w)
      else
        pcall(expandAlias, w, show)
      end
    end
  else
    local s,m = pcall(action)
    if not s then svo.echof("Your do queue %s had a problem:\n  %s", tostring(action), m) end
  end

  if not me.doqueue.repeating then
    table.remove(me.doqueue, 1)
    raiseEvent("svo do changed")
  end

  svo.doworking = false

  return true
end

svo.signals.systemstart:connect(function () svo.addbalanceful("svo check do", sk.check_do) end, 'add balanceful do check at start')

function svo.check_dofree()
  if not bals.balance or not bals.equilibrium or not bals.rightarm or not bals.leftarm or svo.dofreeworking then return end

  if #me.dofreequeue == 0 then return end

  svo.dofreeworking = true

  for _, action in ipairs(me.dofreequeue) do
    if type(action) == 'string' then
      for _,w in ipairs(string.split(action, "%$")) do
        expandAlias(w, false)
      end
    else
      local s,m = pcall(action)
      if not s then svo.echof("Your dofree queue %s had a problem:\n  %s", tostring(action), m) end
    end
  end

  me.dofreequeue = {}
  raiseEvent("svo dofree changed")

  svo.dofreeworking = false
end

svo.signals.systemstart:connect(function () svo.addbalanceless("svo check dofree", svo.check_dofree) end, 'add balanceless dofree check at start')

function svo.undo(what, echoback)
  if what == 'all' then return end

  if #me.doqueue == 0 then
    if echoback then svo.echof("The do queue is empty.") end
  return end

  if what then
    for i in ipairs(me.doqueue) do
      local action = sk.getactiondo(i)
      if type(action) == 'table' then
        action = action.what
      end

      if action == what then
        table.remove(me.doqueue, i)
        if echoback then svo.echof("Removed \"%s\" from the do queue.", action) return end
      end
    end

    if echoback then svo.echof("Don't have \"%s\" in the do queue.", what) end
  else
    local action = sk.getactiondo(1)
    if type(action) == 'table' then
      action = action.what
    end

    if echoback then svo.echof("Removed \"%s\" from the do queue.", action) end
  end
  raiseEvent("svo do changed")
end

function svo.undofree(echoback, what)
  if #me.dofreequeue == 0 then
    if echoback then svo.echof("The dofree queue is empty.") end
  return end

  if what then
    for i,v in ipairs(me.dofreequeue) do
      if v == what then
        table.remove(me.dofreequeue, i)
        if echoback then svo.echof("Removed \"%s\" from the dofree queue.", v) return end
      end
    end

    if echoback then svo.echof("Don't have \"%s\" in the dofree queue.", what) end
  else
    what = table.remove(me.dofreequeue, 1)
    if echoback then svo.echof("Removed \"%s\" from the dofree queue.", what) end
  end
  raiseEvent("svo dofree changed")
end

function svo.undoall(echoback)
  me.doqueue = {}
  if echoback then svo.echof("Do queue completely cleared.") end
  raiseEvent("svo do changed")
end

function svo.undoallfree(echoback)
  me.dofreequeue = {}
  if echoback then svo.echof("Dofree queue completely cleared.") end
  raiseEvent("svo dofree changed")
end

function svo.doshow()
  svo.echof("Actions left in the dofree queue (%d): %s", #me.dofreequeue, svo.safeconcat(me.dofreequeue, ", "))
  svo.echof("Actions left in the do queue (%d): %s", #me.doqueue, svo.safeconcat(me.doqueue, ", "))
  if me.dorepeat then svo.echof("Do-Repeat is enabled.") end
end

end -- end of svo dor loader

if svo.systemloaded then svo.loader.dor() end
```
</details>


  <summary>Modified Curing Skeleton</summary>
  
  Original (2011-2014) Credit to Vadim Peretokin, 2015+ lots of us
  
  ```lua
  svo = svo or {}; svo.loader = svo.loader or {}
svo.modules_version = svo.modules_version or {}
svo.modules_version["svo (curing skeleton, controllers, action system)"] = 1
svo.loader.sk = function()

local sys, affs, defdefup, defkeepup, signals = svo.sys, svo.affs, svo.defdefup, svo.defkeepup, svo.signals
local deepcopy, conf, sk, me, defs, defc = svo.deepcopy, svo.conf, svo.sk, svo.me, svo.defs, svo.defc
local defences, stats, cnrl, rift = svo.defences, svo.stats, svo.cnrl, svo.rift
local bals, pipes, watch = svo.bals, svo.pipes, svo.watch

sk.affqueueslow = {}
sk.defqueueslow = {}
sk.queuecount = sk.queuecount or 0
sk.priolength = sk.priolength or {affqueue = 0, affqueueslow = 0, defqueue = 0, defqueueslow = 0}

-- misc functions
function svo.errorf(...)
  error(string.format(...))
end

function svo.echon(...)
  local function wrapper(...) decho(string.format(...)) end
  local status, result = pcall(wrapper, ...)
  if not status then error(result, 2) end
  echo("\n")
end

function svo.contains(t, value)
  svo.assert(type(t) == 'table', "svo.contains wants a table!")

  for k, v in pairs(t) do
    if v == value then
      return k
    end
  end

  return false
end

svo.sk.checking_herb_ai = function()
  return (svo.doingaction'checkparalysis' or svo.doingaction'checkasthma' or svo.doingaction'checkimpatience') and true or false
end

svo.force_send = _G.send

-- new incoming balances that are tracked between the lines and the prompt
svo.newbals = {}

-- checks

-- sip check
local healthchecks = {
  healhealth = {p = svo.dict.healhealth},
  healmana = {p = svo.dict.healmana}
}

-- build a table of all the things we need to do with their priority numbers,
-- sort it, and do the topmost thing.
svo.check_sip = function(sync_mode)
  -- can we even sip?
  if not bals.sip or svo.usingbal('sip') or affs.stun or affs.unconsciousness or affs.sleep or affs.anorexia then
      return
  end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.sip and j.p.sip.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.sip.aspriority or j.p.sip.spriority
      end
    end
  end

  check(affs)
  check(healthchecks)

  -- have nada?
  if not next(prios) then return end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].sip) else
    return svo.dict[svo.getHighestKey(prios)].sip end
end

-- purgative check: needs to be asynced as well
svo.check_purgative = function(sync_mode)
  -- can we even sip?
  if not bals.purgative or svo.usingbal('purgative') or affs.stun or affs.unconsciousness or affs.sleep or affs.anorexia then
      return
  end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    local gotsomething = false
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.purgative and j.p.purgative.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.purgative.aspriority or j.p.purgative.spriority
        gotsomething = true
      end
    end

    return gotsomething
  end

  check(affs)

  if sys.deffing or conf.keepup then
    check(svo.dict_purgative)
  end

  -- have nada?
  if not next(prios) then return end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].purgative) else
    return svo.dict[svo.getHighestKey(prios)].purgative end
end


-- salve check
svo.check_salve = function(sync_mode)
  -- can we even use salves?
  if not bals.salve or svo.usingbal('salve') or
    affs.sleep or affs.stun or affs.unconsciousness or affs.slickness then
      return
  end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.salve and j.p.salve.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.salve.aspriority or j.p.salve.spriority
      end
    end
  end

  check(affs)
  if sys.deffing or conf.keepup then check(svo.dict_salve_def) end

  -- have nada?
  if not next(prios) then return false end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].salve) else
    return svo.dict[svo.getHighestKey(prios)].salve end
end

-- herb check

-- build a table of all the things we need to do with their priority numbers,
-- sort it, and do the topmost thing.
svo.check_herb = function(sync_mode)
  -- can we even eat?
  if not bals.herb or svo.usingbal('herb') or affs.sleep
    or affs.stun or affs.unconsciousness or svo.sacid or affs.anorexia
    or (conf.aillusion and conf.waitherbai and sk.checking_herb_ai()) then
      return
  end

  -- get all prios in the list
  local prios = {}
  local function check (what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.herb and j.p.herb.isadvisable() and not svo.ignore[i]
        -- make sure that we can outrift things, or if we can't, we have the herb in our inventory
        and (sys.canoutr or sk.can_eat_for(j.p.herb)) then
          prios[i] = (not sync_mode) and j.p.herb.aspriority or j.p.herb.spriority
      end
    end
  end

  check(affs)
  if sys.deffing or conf.keepup then check(svo.dict_herb) end

  -- have nada?
  if not next(prios) then return false end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].herb) else
    return svo.dict[svo.getHighestKey(prios)].herb end
end

-- misc check

-- build a table of all the things we need to do with their priority numbers,
-- sort it, and do the topmost thing.

-- this is just in case we're checking amnesia only
local amnesias = {
  amnesia = {p = svo.dict.amnesia},
  fear    = {p = svo.dict.fear},
}
svo.check_misc = function(sync_mode, onlyamnesia)
  -- we -don't- check for sleep here, but a bit lower down - so waking can be on a misc
  if affs.stun or affs.unconsciousness then
    return
  end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.misc and j.p.misc.isadvisable() and not svo.ignore[i] and not svo.doingaction (i) and (not affs.sleep or j.p.misc.action_name == 'sleep') then
        prios[i] = (not sync_mode) and j.p.misc.aspriority or j.p.misc.spriority
      end
    end
  end

  if not onlyamnesia then
    check(affs)
    check(svo.dict_misc)
    if sys.deffing or conf.keepup then check(svo.dict_misc_def) end
  else
    check(amnesias)
  end

  -- have nada?
  if not next(prios) then return end

  -- otherwise, do the highest! Also go down the list in priorities in case you need to dontbatch
  if not sync_mode then
    local set = svo.index_map(prios)

    local highest, lowest = svo.getBoundary(prios)

    local dontbatch
    for i = highest, lowest, -1 do
      if set[i] then
        if not svo.dict[set[i]].misc.dontbatch or not dontbatch then
          svo.doaction(svo.dict[set[i]].misc)

          if svo.dict[set[i]].misc.dontbatch then dontbatch = true end
        end
      end
    end
  else
    -- otherwise, do the highest!
    return svo.dict[svo.getHighestKey(prios)].misc
  end
end

local check_for_asthma = {
  checkasthma = {p = svo.dict.checkasthma}
}
svo.check_smoke = function(sync_mode)
  if not bals.smoke or affs.stun or affs.unconsciousness or affs.sleep or affs.asthma or affs.mucous then
    return
  end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.smoke and j.p.smoke.isadvisable() and not svo.ignore[i] and not svo.doingaction(i) then
        prios[i] = (not sync_mode) and j.p.smoke.aspriority or j.p.smoke.spriority
      end
    end
  end

  check(affs)
  if svo.affsp.asthma then check(check_for_asthma) end
  if sys.deffing or conf.keepup then check(svo.dict_smoke_def) end

  -- have nada?
  if not next(prios) then return end
	
  if not sync_mode then
        svo.doaction(svo.dict[svo.getHighestKey(prios)].smoke)
  else
    -- otherwise, do the highest!
    return svo.dict[svo.getHighestKey(prios)].smoke
  end
	
end

svo.check_moss = function(sync_mode)
  -- can we even sip?
  if not conf.moss or svo.usingbal('moss') or affs.stun or affs.unconsciousness or not bals.moss
    or affs.sleep or affs.anorexia then
      return
  end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.moss and j.p.moss.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.moss.aspriority or j.p.moss.spriority
      end
    end
  end

  if not conf.secondarymoss then check(healthchecks) end

  -- have nada?
  if not next(prios) then return end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].moss) else
    return svo.dict[svo.getHighestKey(prios)].moss end
end

svo.check_focus = function(sync_mode)
  -- can we even focus?
  if not next(affs) or svo.usingbal('focus') or affs.stun or affs.unconsciousness or not bals.focus
    or affs.sleep or not svo.can_usemana() or not conf.focus or stats.currentwillpower <= 75
    or affs.impatience or affs.inquisition or (affs.cadmus and not conf.focuswithcadmus) then
      return
  end

  local wont_heal_this = sk.wont_heal_this

  -- get all prios in the list
  local prios = {}
  for i, j in pairs(affs) do
    if not (conf.serverside and svo.serverignore[i]) and j.p.focus and (not affs.cadmus or (conf.focuswithcadmus and me.cadmusaffs[i])) and j.p.focus.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.focus.aspriority or j.p.focus.spriority
    end
  end

  -- have nada?
  if not next(prios) then return end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].focus) else
    return svo.dict[svo.getHighestKey(prios)].focus end
end


-- lifevision system

-- if something was added here, that means it was validated via other
-- means already - all we need to do now is to check if we had lifevision
-- catch the line or no.

-- other_action means do something else than default when done
-- arg is the argument to pass either to the default action
-- lineguard is how many lines this should be across - ineffective with vconfig batch
function svo.lifevision.add(what, other_action, arg, lineguard)
  svo.lifevision.l:set(what.name, {
    p = what,
    other_action = other_action,
    arg = arg
  })

  if lineguard and (not sys.lineguard or sys.lineguard > lineguard) then -- remember the smallest one, because if we have two conflicts, the smallest one is most valid
    sys.lineguard = lineguard
  end

  svo.debugf("svo.lifevision: %s added with '%s' call (%s)%s", tostring(what.name), other_action and other_action or 'default', tostring(arg), (lineguard and " lg: "..lineguard or ""))

  if not sys.sync then return end
  if svo.actions[what.name] and what.balance ~= 'aff' and what.balance ~= 'gone' and color_table[conf.slowcurecolour] then
    selectCurrentLine()
    fg(conf.slowcurecolour)
    resetFormat()
    deselect()
  end
end

-- special: adds something where required, ie, first position in the queue
-- was necessary to have blackout be above everything else so stun AI doesn't slow it down 'till next prompt
function svo.lifevision.addcust(what, where, other_action, arg)
  svo.assert(what, "svo.lifevision.addcust wants an argument")
  svo.lifevision.l:insert(where, what.name, {
    p = what,
    other_action = other_action,
    arg = arg
  })
  svo.debugf("svo.lifevision: %s added (pos %d) with '%s' call (%s)", tostring(what.name), where, other_action and other_action or 'default', tostring(arg))
end

-- returns the current lineguard that's set or nil
function svo.lifevision.getlineguard()
  return sys.lineguard
end

function svo.lifevision.clearlineguard()
  sys.lineguard = nil
end

local function run_through_actions()
  for _,j in svo.lifevision.l:iter() do
    if not sk.stopprocessing then
      svo.actionfinished(j.p, j.other_action, j.arg)
    else
      svo.actionclear(j.p)
    end
  end
end

function svo.lifevision.validate()
  -- take a line off the paragraph_length if the game's curing went off, as it is a 'meta' message and shouldn't be counted
  local paragraph_length = svo.paragraph_length
  if sk.sawcuring() then paragraph_length = paragraph_length - 1 end

  -- batch needs to disable lineguard, as commands come at once then. Plus, illusions aren't as prevalent anymore since serverside curing is completely immune to them
  if sys.flawedillusion or (not conf.batch and sys.lineguard and paragraph_length > sys.lineguard) then
    if sys.not_illusion then
      svo.debugf("cancelled illusion")
      run_through_actions()

      moveCursor(0, getLineNumber()-1)
      moveCursor(#getCurrentLine(), getLineNumber())
      insertLink(" (!i)", '', (type(sys.not_illusion) == 'string' and sys.not_illusion or "Cancelled detected 'illusion' due to script override."))
      sys.not_illusion = false
    else
      svo.debugf("got an illusion")

      for _,j in svo.lifevision.l:iter() do
        svo.actionclear(j.p)
      end

      if sys.lineguard and not sys.flawedillusion then
        svo.debugf("svo.lifevision.validate: paragraph_length %d, sys.lineguard %d", paragraph_length, sys.lineguard)
        moveCursor(0, getLineNumber()-1)
        moveCursor(#getCurrentLine(), getLineNumber())
        insertLink(" (i)", '', "Ignored this whole illusion because the line(s) present need to be in their own.")
        moveCursorEnd()
      end
    end
    sys.flawedillusion, me.haveillusion = false, false
  else
    run_through_actions()
  end
  svo.lifevision.l = svo.pl.OrderedMap()
  sk.stopprocessing = nil
  sys.lineguard = false
end

svo.checkanyaffs = function (...)
  local t = {...}
  for i=1,#t do
    local j = t[i]

    if affs[j.name] then
    return j end
  end
end

-- balanceful check
svo.check_balanceful_acts = function(sync_mode)
  if affs.sleep or affs.stun or affs.unconsciousness or not bals.balance or not bals.equilibrium or not bals.rightarm or not bals.leftarm or (svo.me.class == 'Druid' and not bals.hydra)
  then return end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.physical.balanceful_act and j.p.physical.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.physical.aspriority or j.p.physical.spriority
      end
    end
  end

  check(svo.dict_balanceful)

  if sys.deffing or conf.keepup then
    check(svo.dict_balanceful_def)
  end

  -- have nada?
  if not next(prios) then return false end

  -- otherwise, do the highest!
  if not sync_mode then
    svo.doaction(svo.dict[svo.getHighestKey(prios)].physical) else
    return svo.dict[svo.getHighestKey(prios)].physical end

  return true
end

-- balanceless check
svo.check_balanceless_acts = function(sync_mode)
  if affs.sleep or affs.stun or affs.unconsciousness or not bals.balance or not bals.equilibrium or not bals.rightarm or not bals.leftarm or (svo.me.class == 'Druid' and not bals.hydra)
   then return end

  -- get all prios in the list
  local prios = {}
  local function check(what)
    local gotsomething = false

    for i, j in pairs(what) do
      if not (conf.serverside and svo.serverignore[i]) and j.p.physical.balanceless_act and j.p.physical.isadvisable() and not svo.ignore[i] then
        prios[i] = (not sync_mode) and j.p.physical.aspriority or j.p.physical.spriority
        gotsomething = true
      end
    end

    return gotsomething
  end

  check(svo.dict_balanceless)

  if sys.deffing or conf.keepup then
    check(svo.dict_balanceless_def)
  end

  -- have nada?
  if not next(prios) then return end

  -- otherwise, do the highest!
  if not sync_mode then
    local set = svo.index_map(prios)

    local highest, lowest = svo.getBoundary(prios)
    for i = highest, lowest, -1 do
      if set[i] then
        svo.doaction(svo.dict[set[i]].physical)
      end
    end
  else
    return svo.dict[svo.getHighestKey(prios)].physical
  end

  return true
end

local balanceless = svo.balanceless or {}
local balanceful = svo.balanceful or {}

function svo.sk.balance_controller()
  if sys.balanceid == sys.balancetick then return end

  if not (bals.balance and bals.equilibrium) or (affs.webbed or affs.bound or affs.transfixed or affs.roped or affs.impale or affs.paralysis or affs.sleep) then return end

  -- loop through all balanceless functions
  for _, f in pairs(balanceless) do
    f()
  end

-- loop through balanceful actions until we get one that takes bal or eq
  local r
  for _,f in pairs(balanceful) do
    r = f()
    if r then
      if sys.actiontimeoutid then killTimer(sys.actiontimeoutid) end
      if type(r) == 'number' then
        sys.actiontimeoutid = tempTimer(r, function () sys.balancetick = sys.balancetick + 1; svo.make_gnomes_work() end)
      elseif conf.lag and conf.lag == 4 then
        -- 24 does it right away!
        sys.actiontimeoutid = tempTimer(60*60*23, function () sys.balancetick = sys.balancetick + 1; svo.make_gnomes_work() end)
      else
        sys.actiontimeoutid = tempTimer(sys.actiontimeout, function () sys.balancetick = sys.balancetick + 1; svo.make_gnomes_work() end)
      end

      sys.balanceid = sys.balancetick
      break
    end
  end
end

function svo.addbalanceless(name, func)
  svo.assert(name and func, "svo.addbalanceless: both name and function are required")
  svo.assert(type(func) == 'function', "svo.addbalanceless: function needs to be an actual function, while you gave it a "..type(func))

  balanceless[name] = func
end

function svo.removebalanceless(name)
  balanceless[name] = nil
end

function svo.addbalanceful(name, func)
  svo.assert(name and func, "svo.addbalanceful: both name and function are required")
  svo.assert(type(func) == 'function', "svo.addbalanceful: second argument has to be a function (you gave it a "..type(func)..")")

  balanceful[name] = func
end

function svo.removebalanceful(name)
  balanceful[name] = nil
end

function svo.clearbalanceful()
  balanceful = {}
  svo.addbalanceful("svo check do", sk.check_do)
  raiseEvent("svo balanceful ready")
end

function svo.clearbalanceless()
  balanceless = {}
  svo.addbalanceless("svo check dofree", svo.check_dofree)
  raiseEvent("svo balanceless ready")
end

tempTimer(0, function ()
  raiseEvent("svo balanceless ready")
  raiseEvent("svo balanceful ready")
end)

-- svo Got prompt
-- DO WORK!

-- utils
local function find_highest_action(tbl)
  local result
  local highest = 0
  for _,j in pairs(tbl) do
    if j.spriority > highest then
      highest = j.spriority
      result = j
    end
  end

  return result
end

local workload = {svo.check_salve, svo.check_focus, svo.check_sip, svo.check_purgative,
            svo.check_smoke, svo.check_herb, svo.check_moss, svo.check_misc,
            svo.check_balanceless_acts, svo.check_balanceful_acts}

-- real functions
local function work_slaves_work()
  -- in async, ask each bal to do its action

  svo.check_misc(false, true) -- amnesia & fear only

  svo.check_focus()
  svo.check_salve()

  svo.check_sip()
  svo.check_purgative()
  svo.check_smoke()
  svo.check_herb()

  svo.check_misc() -- fails for amnesia, but works for Priest Healing...

  svo.check_moss()

  svo.check_balanceless_acts()

  -- if the system didn't use bal, let it be used for other things.
  if not svo.check_balanceful_acts() and not svo.will_take_balance() then sk.balance_controller() end

  -- serverside prios: eat, apply, smoke, focus
end

svo.make_gnomes_work_async = function()
  if conf.paused then return end

  signals.sysdatasendrequest:block(cnrl.processusercommand)

  if conf.commandecho and (conf.commandechotype == 'fancy' or conf.commandechotype == 'fancynewline') then
    send = svo.fancysend

    -- insert expandAlias (used in dofree, dor and similar) into the current batch, breaking the batch up in the process
    local oldexpandAlias = expandAlias
    if conf.batch then
      expandAlias = function(command, show)
        svo.sendc({ func = oldexpandAlias, args = {command, show} })
      end
    end

    work_slaves_work()
    -- commands are echoed by fancysendall() in onpromptr() in case of a prompt from the game, otherwise echo them right away if from a forced svo.make_gnomes_work()
    if not sk.processing_prompt then svo.fancysendall() end
    send = svo.oldsend

    if conf.batch then
      expandAlias = oldexpandAlias
    end
  else
    work_slaves_work()
  end

  signals.sysdatasendrequest:unblock(cnrl.processusercommand)
end

svo.make_gnomes_work_sync = function()
  sk.syncdebug = false
  if conf.paused or svo.sacid then return end

  signals.sysdatasendrequest:block(cnrl.processusercommand)

  -- if we're already doing an action that is not of an 'waitingfor' type, don't do anything!
  -- logic: if next returns nil,
  local result
  for balance,actions in pairs(svo.bals_in_use) do
    if balance ~= 'waitingfor' and balance ~= 'gone' and balance ~= 'aff' and next(actions) then result = select(2, next(actions)) break end
  end
  if result then
    svo.debugf("doing %s, quitting for now", result.name)
    sk.syncdebug = string.format("[%s]: Currently doing: %s", getTimestamp(getLineCount()):trim(), result.name)

    signals.sysdatasendrequest:unblock(cnrl.processusercommand)
    return
  end

  sk.gnomes_are_working = true

  local action_list = {}

  --... check for all bals.
  -- in sync, only return values
  for _,j in pairs(workload) do
    result = j(true)
    if result then action_list[result.name] = result end
  end

  local actions = svo.pl.tablex.keys(action_list)
  table.sort(actions, function(a,b)
    return action_list[a].spriority > action_list[b].spriority
  end)

  sk.syncdebug = string.format('[%s]: Feasible actions we\'re currently considering doing (in order): %s', getTimestamp(getLineCount()):trim(), (not next(action_list) and '(none)' or svo.concatand(actions)))

  -- nothing to do =)
  if not next(action_list) then
    sk.gnomes_are_working = false

    signals.sysdatasendrequest:unblock(cnrl.processusercommand)
    return
  end

  if conf.commandecho and conf.commandechotype == 'fancy' then
    send = svo.fancysend
    local oldbatch = conf.batch
    conf.batch = false
    svo.doaction(find_highest_action(action_list))
    -- commands are echoed by fancysendall() in onpromptr() in case of a prompt from the game, otherwise echo them right away if from a forced svo.make_gnomes_work()
    if not sk.processing_prompt then svo.fancysendall() end
    send = svo.oldsend
    conf.batch = oldbatch
  else
    svo.doaction(find_highest_action(action_list))
  end
  sk.gnomes_are_working = false

  signals.sysdatasendrequest:unblock(cnrl.processusercommand)
end

-- default is async
signals.changecuring:connect(function()
  if svo.logging_in then
    svo.make_gnomes_work = function() end
  elseif sys.sync then
    svo.make_gnomes_work = svo.make_gnomes_work_sync
  else
    svo.make_gnomes_work = svo.make_gnomes_work_async
  end
end, "change curing types")
sk.checkaeony()
signals.changecuring:emit()

function svo.send_in_the_gnomes()
  -- at first, deal with lifevision.
  svo.lifevision.validate()
  signals.after_lifevision_processing:emit()

  svo.make_gnomes_work()
end

function svo.update_rift_view()
  local status, msg = pcall(function () svo.mm_create_riftlabel() end)

  if not status then error(msg) end
end

-- retrieve all lines until the last prompt, not including it
function svo.sk.getuntilprompt()
  -- lastpromptnumber would include the prompt, -1 doesn't
  return getLines(svo.lastpromptnumber+1, getLastLineNumber('main'))
end

function svo.sk.makewarnings()
  svo.sk.warnings = {
    lowwillpower = {
      time = 30,
      msg = "Warning: your <253,63,73>willpower is too low"..svo.getDefaultColor().."! Need to regen some - otherwise you can't fight well (no clot, focus, and so on)."
    },
    somewhatreavable = {
      time = 10,
      msg = "Warning: you have two humours - an Alchemists <253,63,73>Reave"..svo.getDefaultColor().." will take 10s",
    },
    nearlyreavable = {
      time = 5,
      msg = "Warning: you have three humours - an Alchemists <253,63,73>Reave"..svo.getDefaultColor().." will take 8s",
    },
    reavable = {
      time = 5,
      msg = "Warning: you have all four humours - an Alchemists <253,63,73>Reave"..svo.getDefaultColor().." will only take 4s",
    },
    dismemberable = {
      time = 5,
      msg = "Warning: you're bound and impaled - you can be instakilled! (dismember)"
    },
    cantclotmana = {
      time = 10,
      msg = "Going temporarily pause clotting your mana bleeding, your health is below corruptedhealthmin"
    },
    golemdestroyable = {
      time = 5,
      msg = "Warning: your flesh is melting - you can be instakilled! (golem destroy)"
    },
    pulpable = {
      time = 5,
      msg = "Warning: prone and serious concussion - you can be installed! (pulp)"
    },
    badaeon = {
      time = 5,
      msg = function()
        svo.echof("Warning: your aeon situation is looking bad, you might want to %swalk out%s",
          (not conf.blockcommands and '' or "tsc off and "),
          (conf.org == 'Ashtan' and " and ask for an empress") or
          (conf.org == 'Targossas' and " and ask for a deliver") or
          (conf.org == 'Cyrene' and " and ask for a deliver") or
          ""
        )
      end
    }
  }

  if conf.curemethod == 'transonly' then
    sk.warnings.noelmid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>cinnabar"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.novalerianid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>realgar"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.noskullcapid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>malachite"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.emptyvalerianpipe = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>realgar"..svo.getDefaultColor().." pipe and it's empty! Don't chase balance for a bit",
    }
    sk.warnings.emptyvalerianpipenorefill = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>realgar"..svo.getDefaultColor().." pipe, it's empty, but can't due to blocking afflictions :(",
    }
  elseif conf.curemethod == 'preferconc' then
    sk.warnings.noelmid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>elm"..svo.getDefaultColor().."/<31,31,153>cinnabar"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.novalerianid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>valerian"..svo.getDefaultColor().."/<31,31,153>realgar"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.noskullcapid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>skullcap"..svo.getDefaultColor().."/<31,31,153>malachite"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.emptyvalerianpipe = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>valerian"..svo.getDefaultColor().."/<31,31,153>realgar"..svo.getDefaultColor().." pipe and it's empty! Don't chase balance for a bit",
    }
    sk.warnings.emptyvalerianpipenorefill = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>valerian"..svo.getDefaultColor().."/<31,31,153>realgar"..svo.getDefaultColor().." pipe, it's empty, but can't due to blocking afflictions :(",
    }
  elseif conf.curemethod == 'prefertrans' then
    sk.warnings.noelmid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>cinnabar"..svo.getDefaultColor().."/<31,31,153>elm"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.novalerianid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>realgar"..svo.getDefaultColor().."/<31,31,153>valerian"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.noskullcapid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>malachite"..svo.getDefaultColor().."/<31,31,153>skullcap"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.emptyvalerianpipe = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>realgar"..svo.getDefaultColor().."/<31,31,153>valerian"..svo.getDefaultColor().." pipe and it's empty! Don't chase balance for a bit",
    }
    sk.warnings.emptyvalerianpipenorefill = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>realgar"..svo.getDefaultColor().."/<31,31,153>valerian"..svo.getDefaultColor().." pipe, it's empty, but can't due to blocking afflictions :(",
    }
  else
    sk.warnings.noelmid = {
        time = 20,
        msg = "Warning: need to use your <31,31,153>elm"..svo.getDefaultColor().." pipe and you don't have one!",
      }
    sk.warnings.novalerianid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>valerian"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.noskullcapid = {
      time = 20,
      msg = "Warning: need to use your <31,31,153>skullcap"..svo.getDefaultColor().." pipe and you don't have one!",
    }
    sk.warnings.emptyvalerianpipe = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>valerian"..svo.getDefaultColor().." pipe and it's empty! Don't chase balance for a bit",
    }
    sk.warnings.emptyvalerianpipenorefill = {
      time = 10,
      msg = "Warning: need to refill your <31,31,153>valerian"..svo.getDefaultColor().." pipe, it's empty, but can't due to blocking afflictions :(",
    }
  end
end

if not svo.systemloaded then
  signals.systemstart:add_post_emit(sk.makewarnings, 'post emit systemstart warning')
  signals.orgchanged:add_post_emit(sk.makewarnings, 'post emit orgchanged warning')
end

signals.curemethodchanged:connect(sk.makewarnings, "update warnings for curemethod")

svo.sk.warn = function (what)
  if sk.warnings[what].warned then return end

  tempTimer(sk.warnings[what].time, function() sk.warnings[what].warned = false end)
  sk.warnings[what].warned = true

  moveCursorEnd('main')
  echo("\n")

  if type(sk.warnings[what].msg) == 'function' then
    sk.warnings[what].msg()
  else svo.echof(sk.warnings[what].msg) end

  echo("\n")
end

sk.retardation_count = 0
function svo.sk.retardation_symptom()
  if (affs.retardation or affs.aeon or svo.affsp.retardation or svo.affsp.aeon or svo.affsp.truename) then return end

  sk.retardation_count = sk.retardation_count + 1
  if sk.retardation_count >= 4 then
    if not affs.blackout then
      if not conf.aillusion then
        svo.valid.simpleretardation()
        echo"\n" svo.echof("auto-detected retardation.")
      else
        svo.checkaction(svo.dict.checkslows.aff, true)
        svo.lifevision.add(svo.actions.checkslows_aff.p, nil, 'retardation')
        echo"\n" svo.echof("Maybe we're in retardation - checking it.")
      end
    else
      svo.valid.simpleunknownany(conf.unknownany)
      echo"\n" svo.echof("auto-detection aeon or retardation (going to diagnose to check which)")
    end
    sk.retardation_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 3, function ()
    sk.retardation_count = sk.retardation_count - 1
    if sk.retardation_count < 0 then sk.retardation_count = 0 end
  end)
end

sk.stupidity_count = 0
function svo.sk.stupidity_symptom()

  if conf.serverside then return end

  if affs.stupidity then return end

  sk.stupidity_count = sk.stupidity_count + 1

  if sk.stupidity_count >= 3 then
    svo.valid.simplestupidity()
    echo"\n" svo.echof("auto-detected stupidity.")
    sk.stupidity_count = 0
    return
  end

  tempTimer(svo.syncdelay() + 2, function ()
    sk.stupidity_count = sk.stupidity_count - 1
    if sk.stupidity_count < 0 then sk.stupidity_count = 0 end
  end)
end

sk.illness_constitution_count = 0
function svo.sk.illness_constitution_symptom()
  if not defc.constitution then return end
  if conf.serverside then return end

  if affs.illness_constitution then return end

  sk.illness_constitution_count = sk.illness_constitution_count + 1

  if sk.illness_constitution_count >= 2 then
    svo.valid.simplehypochondria()

    echo"\n" svo.echof("auto-detected hypochondria.")

    sk.illness_constitution_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 3, function ()
    sk.illness_constitution_count = sk.illness_constitution_count - 1
    if sk.illness_constitution_count < 0 then sk.illness_constitution_count = 0 end
  end)
end

sk.transfixed_count = 0
function svo.sk.transfixed_symptom()
  if affs.transfixed then return end
  if conf.serverside then return end

  if affs.transfixed then return end

  sk.transfixed_count = sk.transfixed_count + 1

  if sk.transfixed_count >= 2 then
    svo.valid.simpletransfixed()

    -- supress echo when got hit with it before ai went off
    if not svo.affsp.transfixed then
      echo"\n" svo.echof("auto-detected transfix.")
    end
    sk.transfixed_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 3, function ()
    sk.transfixed_count = sk.transfixed_count - 1
    if sk.transfixed_count < 0 then sk.transfixed_count = 0 end
  end)
end

sk.stun_count = 0
function svo.sk.stun_symptom()
  if affs.stun then return end

  sk.stun_count = sk.stun_count + 1

  if sk.stun_count >= 3 then
    svo.valid.proper_stun()
    echo"\n" svo.echof("auto-detected stun.")
    sk.stun_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.stun_count = sk.stun_count - 1
    if sk.stun_count < 0 then sk.stun_count = 0 end
  end)
end

sk.impale_count = 0
function svo.sk.impale_symptom()
  if conf.serverside then return end

  if affs.impale then return end

  sk.impale_count = sk.impale_count + 1

  if sk.impale_count >= 2 then
    svo.valid.simpleimpale()
    echo"\n" svo.echof("auto-detected impale.")
    sk.impale_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.impale_count = sk.impale_count - 1
    if sk.impale_count < 0 then sk.impale_count = 0 end
  end)
end

sk.aeon_count = 0
function svo.sk.aeon_symptom()
  if affs.aeon then return end

  sk.aeon_count = sk.aeon_count + 1

  if sk.aeon_count >= 2 then
    svo.valid.simpleaeon()
    defs.lost_speed()
    echo"\n" svo.echof("auto-detected aeon.")
    sk.aeon_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.aeon_count = sk.aeon_count - 1
    if sk.aeon_count < 0 then sk.aeon_count = 0 end
  end)
end

sk.paralysis_count = 0
function svo.sk.paralysis_symptom()
  if conf.serverside then return end

  if affs.paralysis then return end

  sk.paralysis_count = sk.paralysis_count + 1

  if sk.paralysis_count >= 2 then
    svo.valid.simpleparalysis()
    echo"\n" svo.echof("auto-detected paralysis.")
    sk.paralysis_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.paralysis_count = sk.paralysis_count - 1
    if sk.paralysis_count < 0 then sk.paralysis_count = 0 end
  end)
end

sk.haemophilia_count = 0
function svo.sk.haemophilia_symptom()
 if affs.haemophilia then return end

  sk.haemophilia_count = sk.haemophilia_count + 1

  if sk.haemophilia_count >= 2 then
    svo.valid.simplehaemophilia()
    echo"\n" svo.echof("haemophilia seems to be real.")
    sk.haemophilia_count = 0
    return
  end

  -- special # 1 - so haemophilia illusions 'can't' happen within 1s
  tempTimer(svo.syncdelay() + 1, function ()
    sk.haemophilia_count = sk.haemophilia_count - 1
    if sk.haemophilia_count < 0 then sk.haemophilia_count = 0 end
  end)
end

sk.webbed_count = 0
function svo.sk.webbed_symptom()
  if conf.serverside then return end

  if affs.webbed then return end

  sk.webbed_count = sk.webbed_count + 1
  if sk.webbed_count >= 2 then
    svo.valid.simplewebbed()
    echo"\n" svo.echof("auto-detected web.")
    sk.webbed_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.webbed_count = sk.webbed_count - 1
    if sk.webbed_count < 0 then sk.webbed_count = 0 end
  end)
end

sk.roped_count = 0
function svo.sk.roped_symptom()
  if conf.serverside then return end

  if affs.roped then return end

  sk.roped_count = sk.roped_count + 1

  if sk.roped_count >= 2 then
    svo.valid.simpleroped()
    echo"\n" svo.echof("auto-detected roped.")
    sk.roped_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.roped_count = sk.roped_count - 1
    if sk.roped_count < 0 then sk.roped_count = 0 end
  end)
end

sk.impaled_count = 0
function svo.sk.impaled_symptom()
  if conf.serverside then return end

  if affs.impale then return end

  sk.impaled_count = sk.impaled_count + 1

  if sk.impaled_count >= 2 then
    svo.valid.simpleimpale()
    echo"\n" svo.echof("auto-detected impale.")
    sk.impaled_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.impaled_count = sk.impaled_count - 1
    if sk.impaled_count < 0 then sk.impaled_count = 0 end
  end)
end


sk.hypochondria_count = 0
function svo.sk.hypochondria_symptom()
  if svo.find_until_last_paragraph(line, 'exact') or affs.hypochondria then return end

  sk.hypochondria_count = sk.hypochondria_count + 1
  
  if sk.hypochondria_count == 2 then
    svo.echof("We might have Hypochondria")
  elseif sk.hypochondria_count >= 3 then
    svo.echof("Enough Afflictions Ticked, Adding Hypochondria")
    svo.valid.simplehypochondria()
    sk.hypochondria_count = 0
  end

  tempTimer(3, function ()
    sk.hypochondria_count = sk.hypochondria_count - 1
    if sk.hypochondria_count < 0 then sk.hypochondria_count = 0 end
  end)
end

sk.unparryable_count = 0
function svo.sk.unparryable_symptom()
  if conf.aillusion and svo.paragraph_length ~= 1 and not svo.find_until_last_paragraph("Your scabbard does not contain your blade, Warrior.", 'exact') and not svo.find_until_last_paragraph("You have not positioned a scabbard on your hip, Warrior.", 'exact') then
    svo.ignore_illusion("not first") return
  elseif affs.unparryable then return end

  sk.unparryable_count = sk.unparryable_count + 1

  if sk.unparryable_count >= 2 then
    sk.cant_parry()
    sk.unparryable_count = 0
    return
  end

  tempTimer(svo.syncdelay() + sys.wait * 2, function ()
    sk.unparryable_count = sk.unparryable_count - 1
    if sk.unparryable_count < 0 then sk.unparryable_count = 0 end
  end)
end

svo.updateaffcount = function (which)
  svo.affl[which.name].count = which.count

  raiseEvent("svo updated aff", which.name, 'count', which.count)
end


-- adds an affliction for the system to be tracking (ie - you are afflicted with it)
-- does not mess with aff.<affliction>s table if the aff is already registered.
-- this is the old internal 'addaff' function that Svof used when it was out of Mudlet
local old_internal_addaff = function (new_aff)
  if not new_aff then svo.debugf("no new, log: %s", debug.traceback()) end
  if affs[new_aff.name] then return end

  local name = new_aff.name

  svo.affs[name] = {
    p = new_aff,
    sw = new_aff.sw or createStopWatch()
  }
  startStopWatch(affs[name].sw)

  -- call the onadded handler if any
  if svo.dict[name].onadded then svo.dict[name].onadded() end

  if not svo.affl[name] then
    svo.affl[name] = { sw = affs[name].sw }
    if svo.affs[name].p.count ~= nil then
      svo.affl[name].count = svo.affs[name].p.count
    end
    signals.svogotaff:emit(name)
    raiseEvent("svo got aff", name)
  end
end
-- this is the old public 'addaff' function that Svof enabled when it was out of Mudlet
local old_public_addaff = function (new_aff)
  svo.assert(type(new_aff) == 'string', "svo.addaff: what aff would you like to add? name must be a string")
  svo.assert(svo.dict[new_aff] and svo.dict[new_aff].aff, "svo.addaff: "..new_aff.." isn't a known aff name")

  if affs[new_aff] then
    return false
  else
    if svo.dict[new_aff].aff and svo.dict[new_aff].aff.forced then
      svo.dict[new_aff].aff.forced()
    elseif svo.dict[new_aff].aff then
      svo.dict[new_aff].aff.oncompleted()
    else
      old_internal_addaff(svo.dict[new_aff])
    end

    signals.after_lifevision_processing:unblock(cnrl.checkwarning)
    sk.checkaeony()
    signals.changecuring:emit()
    svo.codepaste.badaeon()

    return true
  end
end
svo.addaff = function(aff_string_or_table)
  if type(aff_string_or_table) == 'table' then
    old_internal_addaff(aff_string_or_table)
  else
    old_public_addaff(aff_string_or_table)
  end
end
svo.addaffdict = old_internal_addaff
                
-- old internal version of removeaff
svo.rmaff = function (old)
  if type(old) == 'table' then
    for _,aff in pairs(old) do
      svo.rmaff(aff)
    end
    return
  end

  if not affs[old] then return end

  if svo.affl[old] then
    svo.affl[old] = nil
    signals.svolostaff:emit(old)
    raiseEvent("svo lost aff", old)
  end

  -- rmaff can be called on affs that don't exist, that's valid
  local sw = (affs[old] and affs[old].sw or nil)
  affs[old] = nil

  -- call the onremoved handler if any. Should be called after affs is cleaned, because scripts here reply on the 'current' state
  if svo.dict[old].onremoved then
    svo.debugf("calling onremoved for %s", old)
    svo.dict[old].onremoved()
  end

  if conf.showafftimes and sw then
    svo.echoafftime(stopStopWatch(sw), old)
  end
end

-- public version of removeaff. The two should be merged.
svo.removeaff = function (which)
  svo.assert(type(which) == 'string', "svo.removeaff: what aff would you like to remove? name must be a string")
  svo.assert(svo.dict[which] and svo.dict[which].aff, "svo.removeaff: "..which.." isn't a known aff name")

  local removed = false
  if svo.lifevision.l[which..'_aff'] then
    svo.lifevision.l:set(which..'_aff', nil)
    removed = true
  end

  if affs[which] then
    if svo.dict[which].gone then
      svo.dict[which].gone.oncompleted()
    else
      svo.removeaff(which)
    end

    removed = true
  end

  signals.after_lifevision_processing:unblock(cnrl.checkwarning)
  sk.checkaeony()
  signals.changecuring:emit()

  return removed
end

svo.removeafflevel = function (which, amount, keep)
  svo.assert(type(which) == 'string', "svo.removeafflevel: what aff would you like to remove? name must be a string")
  svo.assert(svo.dict[which] and svo.dict[which].aff, "svo.removeafflevel: "..which.." isn't a known aff name")

  local removed = false
  if svo.lifevision.l[which..'_aff'] then
    svo.lifevision.l:set(which..'_aff', nil)
    removed = true
  end

  if affs[which] then
    if svo.dict[which].gone then
      svo.dict[which].gone.general_cure(amount or 1, not keep)
    else
      svo.removeaff(which)
    end

    removed = true
  end

  signals.after_lifevision_processing:unblock(cnrl.checkwarning)
  sk.checkaeony()
  signals.changecuring:emit()

  return removed
end

-- externally available as svo.prompttrigger
sk.onpromptfuncs = {}
function sk.onprompt_beforeaction_add(name, what)
  sk.onpromptfuncs[name] = what
end

sk.onprompt_beforeaction_do = function()
  for name, func in pairs(sk.onpromptfuncs) do
    local s,m = pcall(func)
    if not s then
    svo.debugf("sk.onprompt_beforeaction_do error from %s: %q", name, m)
      echoLink("(e!)", [[echo([=[The problem was: ']]..tostring(name)..[[' prompttrigger failed to work: ']]..tostring( m)..[[']=])]], 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a copy/paste of what you saw if this isn\'t your own function.')
    end
  end
  sk.onpromptfuncs = {}
end
signals.after_lifevision_processing:connect(sk.onprompt_beforeaction_do, "add onprompt_beforeaction_do")

-- externally available as svo.aiprompt
sk.onpromptaifuncs = {}
function sk.onprompt_beforelifevision_add(name, what)
  sk.onpromptaifuncs[name] = what
end

sk.onprompt_beforelifevision_do = function()
  for name, func in pairs(sk.onpromptaifuncs) do
    local s,m = pcall(func)
    if not s then
      echoLink("(e!)", [[echo([=[The problem was: ']]..tostring(name)..[[' aiprompt failed to work: ']]..tostring( m)..[[']=])]], 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a copy/paste of what you saw if this isn\'t your own function.')
    end
  end
  sk.onpromptaifuncs = {}
end
signals.before_prompt_processing:connect(sk.onprompt_beforelifevision_do, "add onprompt_beforelifevision_do")

svo.lostbal_tree = function()
  if bals.tree then tempTimer(0, [[raiseEvent("svo lost balance", 'tree')]]) end
  bals.tree = false
  svo.startbalancewatch('tree')
  if sys.treetimer then killTimer(sys.treetimer) end
  -- if conf.treebalance is set, use that - otherwise use the defaults as setup by conf.efficiency + hardcoded numbers
  local timeout
  if not conf.treebalance or conf.treebalance == 0 then
    timeout = conf.efficiency and (16+svo.getping()) or (40+svo.getping())
  else
    timeout = conf.treebalance
  end
  if affs.ninkharsag then timeout = timeout + 10 end

  sys.treetimer = tempTimer(timeout, [[svo.bals.tree = true;
    svo.echof("Can touch tree again.")
    svo.showprompt()
    raiseEvent("svo got balance", 'tree')]])
end

svo.lostbal_focus = function()
  if not bals.focus then return end

  bals.focus = false
  svo.startbalancewatch('focus')
  sk.focustick = sk.focustick + 1
  local oldfocustick = sk.focustick

  -- respect conf.ai_resetfocusbal while setting a minimum of 8s
  local timeout = conf.ai_resetfocusbal
  if affs.rixil then
    if conf.ai_resetfocusbal < 5 then
      timeout = conf.ai_resetfocusbal + 5
    else
      timeout = 8
    end
  end

  tempTimer(timeout, function ()
    if not bals.focus and sk.focustick == oldfocustick then
      bals.focus = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'focus')
    end
  end)

  raiseEvent("svo lost balance", 'focus')
end

svo.lostbal_shrugging = function()
  if not bals.shrugging then return end

  bals.shrugging = false
  svo.startbalancewatch('shrugging')
  sk.shruggingtick = sk.shruggingtick + 1
  local oldshruggingtick = sk.shruggingtick
  
  --takes 13 to recover
  tempTimer(15+svo.getping(), function ()
    if not bals.shrugging and sk.shruggingtick == oldshruggingtick then
      bals.shrugging = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'shrugging')
    end
  end)

  raiseEvent("svo lost balance", 'shrugging')
end

svo.lostbal_fitness = function()
  if not bals.fitness then return end

  bals.fitness = false
  svo.startbalancewatch('fitness')
  sk.fitnesstick = sk.fitnesstick + 1
  local oldfitnesstick = sk.fitnesstick

  -- takes 9s to recover
  tempTimer(15+svo.getping(), function ()
    if not bals.fitness and sk.fitnesstick == oldfitnesstick then
      bals.fitness = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'fitness')
    end
  end)

  raiseEvent("svo lost balance", 'fitness')
end

svo.lostbal_rage = function()
  if not bals.rage then return end

  bals.rage = false
  svo.startbalancewatch('rage')
  sk.ragetick = sk.ragetick + 1
  local oldragetick = sk.ragetick

  -- takes 9s to recover
  tempTimer(15+svo.getping(), function ()
    if not bals.rage and sk.ragetick == oldragetick then
      bals.rage = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'rage')
    end
  end)

  raiseEvent("svo lost balance", 'rage')
end

svo.lostbal_prayer = function()
  if not bals.prayer then return end

  bals.prayer = false
  svo.startbalancewatch('prayer')
  sk.prayertick = sk.prayertick + 1
  local oldprayertick = sk.prayertick
  local reset = conf.ai_resetprayerbal

  tempTimer(3+svo.getping(), function ()
    if not bals.prayer and sk.prayertick == oldprayertick then
      bals.prayer = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'prayer')
    end
  end)

  raiseEvent("svo lost balance", 'prayer')
end

svo.lostbal_voice = function()
  if not bals.voice then return end

  bals.voice = false
  svo.startbalancewatch('voice')
  sk.voicetick = sk.voicetick + 1
  local oldvoicetick = sk.voicetick

  tempTimer(10+svo.getping(), function ()
    if not bals.voice and sk.voicetick == oldvoicetick then
      bals.voice = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'voice')
    end
  end)

  raiseEvent("svo lost balance", 'voice')
end

svo.lostbal_sip = function()
  bals.sip = false
  svo.startbalancewatch('sip')
  sk.siptick = sk.siptick + 1
  local oldsiptick = sk.siptick

  -- multiply by 2 if we have addiction
  local lostbalance = conf.ai_resetsipbal
  if affs.addiction then lostbalance = lostbalance * 2 end 

  tempTimer(lostbalance, function ()
    if not bals.sip and sk.siptick == oldsiptick then
      bals.sip = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'sip')
    end
  end)

  raiseEvent("svo lost balance", 'sip')
end

svo.lostbal_herb = function(noeffect, mickey)
  bals.herb = false
  svo.startbalancewatch('herb')
  sk.herbtick = sk.herbtick + 1
  local oldherbtick = sk.herbtick

  tempTimer(conf.ai_resetherbbal, function ()
    if not bals.herb and sk.herbtick == oldherbtick then
      bals.herb = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'herb')
    end
  end)

  watch['bal_herb'] = watch['bal_herb'] or createStopWatch()
  startStopWatch(watch['bal_herb'])

  -- voided gives us the balance quick enough
  if (affs.voided and noeffect) then raiseEvent("svo lost balance", 'herb') return end

  watch.herb_block = watch.herb_block or createStopWatch()
  startStopWatch(watch.herb_block)

  if sk.blockherbbal then killTimer(sk.blockherbbal) end
  -- mickey steals bal for .8s
  sk.blockherbbal = tempTimer((mickey and .5 or conf.ai_minherbbal), function ()
    sk.blockherbbal = nil
  end)

  raiseEvent("svo lost balance", 'herb')
end

svo.lostbal_salve = function()
  bals.salve = false
  svo.startbalancewatch('salve')
  sk.salvetick = sk.salvetick + 1
  local oldsalvetick = sk.salvetick

  tempTimer(conf.ai_resetsalvebal, function ()
    if not bals.salve and sk.salvetick == oldsalvetick then
      bals.salve = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'salve')
    end
  end)

  raiseEvent("svo lost balance", 'salve')
end

svo.lostbal_moss = function()
  bals.moss = false
  svo.startbalancewatch('moss')
  sk.mosstick = sk.mosstick + 1
  local oldmosstick = sk.mosstick

  tempTimer(conf.ai_resetmossbal, function ()
    if not bals.moss and sk.mosstick == oldmosstick then
      bals.moss = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'moss')
    end
  end)

  raiseEvent("svo lost balance", 'moss')
end

svo.lostbal_purgative = function()
  bals.purgative = false
  svo.startbalancewatch('purgative')
  sk.purgativetick = sk.purgativetick + 1
  local oldpurgativetick = sk.purgativetick

  tempTimer(conf.ai_resetpurgativebal, function ()
    if not bals.purgative and sk.purgativetick == oldpurgativetick then
      bals.purgative = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'purgative')
    end
  end)

  raiseEvent("svo lost balance", 'purgative')
end

svo.lostbal_smoke = function()
  bals.smoke = false
  svo.startbalancewatch('smoke')
  sk.smoketick = sk.smoketick + 1
  local oldsmoketick = sk.smoketick

  tempTimer(conf.ai_resetsmokebal, function ()
    if not bals.smoke and sk.smoketick == oldsmoketick then
      bals.smoke = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'smoke')
    end
  end)

  watch['bal_smoke'] = watch['bal_smoke'] or createStopWatch()
  startStopWatch(watch['bal_smoke'])

  raiseEvent("svo lost balance", 'smoke')
end

svo.lostbal_dragonheal = function()
  bals.dragonheal = false
  svo.startbalancewatch('dragonheal')
  sk.dragonhealtick = sk.dragonhealtick + 1
  local olddragonhealtick = sk.dragonhealtick

  -- dragonheal bal is quite long, add a bit of variation on it
  tempTimer(conf.ai_resetdragonhealbal+svo.getping(), function ()
    if not bals.dragonheal and sk.dragonhealtick == olddragonhealtick then
      bals.dragonheal = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'dragonheal')
    end
  end)

  raiseEvent("svo lost balance", 'dragonheal')
end

if svo.haveskillset('terminus') then
svo.lostbal_word = function()
  if not bals.word then return end

  bals.word = false
  svo.startbalancewatch('word')
  sk.wordtick = sk.wordtick + 1
  local oldwordtick = sk.wordtick

  tempTimer(17+svo.getping(), function ()
    if not bals.word and sk.wordtick == oldwordtick then
      bals.word = true
      svo.make_gnomes_work()
      raiseEvent("svo got balance", 'word')
    end
  end)

  raiseEvent("svo lost balance", 'word')
end
end

if svo.haveskillset('anathema') then
svo.lostbal_anathema = function()
  if not bals.anathema then return end
  bals.anathema = false
  raiseEvent("svo lost balance", 'anathema')
  end
end

function sk.doingstuff_inslowmode()
  local result
  for balance,actions in pairs(svo.bals_in_use) do
    if balance ~= 'waitingfor' and balance ~= 'gone' and balance ~= 'aff' and next(actions) then result = select(2, next(actions)) break end
  end
  if result then return true end
end

function sk.checkwillpower()
  if stats.currentwillpower <= 1000 and not sk.lowwillpower then
    sk.lowwillpower = true
    sk.warn('lowwillpower')

    svo.can_usemana = function()
      return (stats.currentmana > sys.manause and stats.currentwillpower >= 100 and not svo.doingaction ('nomana'))
    end

  -- amounts differ so we don't toggle often
  elseif stats.currentwillpower > 1500 and sk.lowwillpower then
    sk.lowwillpower = false

    svo.can_usemana = function()
      return (stats.currentmana > sys.manause and not svo.doingaction ('nomana'))
    end
  end
end

sk.limbnames = {
  rightarm = true,
  leftarm = true,
  leftleg = true,
  rightleg = true,
  torso = true,
  head = true
}

function svo.sk.increase_lagconf()
  -- don't go above 3, 4 is reserved for do really
  if conf.lag >= 3 then return end

  if sk.lag_tickedonce and not sk.increasedlag then
    conf.lag = conf.lag+1
    echo"\n" svo.echof("auto-increased the lag tolerance level to %d.", conf.lag)
    raiseEvent("svo config changed", 'lag')
    sk.increasedlag = true
    cnrl.update_wait()

    if sys.reset_laglevel then killTimer(sys.reset_laglevel) end
    sys.reset_laglevel = tempTimer(30, function ()
      if not svo.wait_tbl[conf.lag-1] then return end

      local variance = getNetworkLatency()*2+getNetworkLatency()
      for i = 0, #svo.wait_tbl do
        if variance <= svo.wait_tbl[i].n then
          conf.lag = i
          cnrl.update_wait()
          echo"\n" svo.echof("automatically reset lag tolerance down to %d.", conf.lag)
          raiseEvent("svo config changed", 'lag')
          break
        end
      end
    end)
  else
    sk.lag_tickedonce = tempTimer(10, function () sk.lag_tickedonce = nil; sk.increasedlag = true end)
  end
end

if svo.haveskillset('metamorphosis') then
function svo.sk.clearmorphs()
  local morphs
  if svo.me.class == 'Druid' then
    morphs = {'squirrel', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'bear', 'nightingale', 'elephant', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm', 'wyvern', 'hydra'}
  else
    morphs = {'squirrel', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'basilisk', 'bear', 'nightingale', 'elephant', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm'}
  end
  for _, morph in ipairs(morphs) do
    if defc[morph] then
      defences.lost(morph)
    end
  end
end

function svo.sk.inamorph()
  local t
if svo.me.class == 'Druid' then
  t = {'squirrel', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'bear', 'nightingale', 'elephant', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm', 'wyvern', 'hydra'}
else
  t = {'squirrel', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'basilisk', 'bear', 'nightingale', 'elephant', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm'}
end
  for i = 1, #t do
    if defc[t[i]] then return true end
  end

  return false
end

function svo.sk.validmorphskill(name)
  local morphs
if svo.me.class == 'Druid' then
  morphs = {'squirrel', 'powers', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'bear', 'bonding', 'nightingale', 'elephant', 'transmorph', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm', 'affinity', 'wyvern', 'hydra', 'truemorph'}
else
  morphs = {'squirrel', 'powers', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'basilisk', 'bear', 'bonding', 'nightingale', 'elephant', 'transmorph', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm', 'affinity', 'truemorph'}
end

  for _, morph in ipairs(morphs) do
    if name:lower() == morph then return true end
  end

  return false
end

function svo.sk.inamorphfor(defence)
  if not sk.morphsforskill[defence] then return false end

  for i = 1, #sk.morphsforskill[defence] do
    if defc[sk.morphsforskill[defence][i]] then return true end
  end

  return false
end

function svo.sk.updatemorphskill()
  sk.morphsforskill = {}
if svo.me.class == 'Druid' then
  sk.morphsforskill.elusiveness = { 'hyena', 'wolverine' }
else
  sk.morphsforskill.elusiveness = { 'basilisk', 'hyena', 'wolverine', 'jaguar' }
end
if svo.me.class == 'Druid' then
  sk.morphsforskill.fitness = { 'wolf', 'cheetah', 'hyena', 'elephant', 'wyvern', 'hydra' }
else
  sk.morphsforskill.fitness = { 'wolf', 'cheetah', 'hyena', 'elephant', 'jaguar'}
end
if svo.me.class == 'Druid' then
  sk.morphsforskill.flame = { 'wyvern' }
else
  sk.morphsforskill.flame = { 'wyvern', 'basilisk' }
end
  sk.morphsforskill.lyre = { 'nightingale' }
if svo.me.class == 'Druid' then
  sk.morphsforskill.nightsight = { 'wildcat', 'wolf', 'cheetah', 'owl', 'hyena', 'condor', 'wolverine', 'eagle', 'icewyrm', 'wyvern', 'hydra' }
else
  sk.morphsforskill.nightsight = { 'wildcat', 'wolf', 'cheetah', 'owl', 'hyena', 'condor', 'wolverine', 'jaguar', 'eagle', 'icewyrm' }
end
  sk.morphsforskill.rest = { 'sloth' }
if svo.me.class == 'Druid' then
  sk.morphsforskill.resistance = { 'hydra' }
else
  sk.morphsforskill.resistance = { 'basilisk', 'jaguar' }
end
if svo.me.class == 'Druid' then
  sk.morphsforskill.stealth = { 'hyena' }
else
  sk.morphsforskill.stealth = { 'basilisk', 'hyena', 'jaguar' }
end
if svo.me.class == 'Druid' then
  sk.morphsforskill.temperance = { 'icewyrm', 'wyvern', 'hydra' }
else
  sk.morphsforskill.temperance = { 'icewyrm' }
end
if svo.me.class == 'Druid' then
  sk.morphsforskill.vitality = { 'bear', 'elephant', 'icewyrm', 'wyvern', 'hydra' }
else
  sk.morphsforskill.vitality = { 'bear', 'elephant', 'jaguar', 'icewyrm' }
end

  sk.skillmorphs = {}
  for skill, t in pairs(sk.morphsforskill) do
    for _, morph in ipairs(t) do
      sk.skillmorphs[morph] = sk.skillmorphs[morph] or {}
      sk.skillmorphs[morph][skill] = true
    end
  end

  local newskillmorphs = {}
  local morphlist
if svo.me.class == 'Druid' then
  morphlist = {'squirrel', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'bear', 'nightingale', 'elephant', 'wolverine', 'eagle', 'gorilla', 'icewyrm', 'wyvern', 'hydra'}
else
  morphlist = {'squirrel', 'wildcat', 'wolf', 'turtle', 'jackdaw', 'cheetah', 'owl', 'hyena', 'condor', 'gopher', 'sloth', 'basilisk', 'bear', 'nightingale', 'elephant', 'wolverine', 'jaguar', 'eagle', 'gorilla', 'icewyrm'}

end
  for _, morph in pairs(morphlist) do
    newskillmorphs[morph] = sk.skillmorphs[morph]
    if svo.conf.morphskill == morph then break end
  end
  sk.skillmorphs = newskillmorphs

  sk.morphsforskill = {}
  for morph, t in pairs(sk.skillmorphs) do
    for def, _ in pairs(t) do
      sk.morphsforskill[def] = sk.morphsforskill[def] or {}
      sk.morphsforskill[def][#sk.morphsforskill[def]+1] = morph
    end
  end

  do -- sort morph names in sk.morphsforskill such that the first one has the most morphs
    local morphdefs = {}
    for morph, t in pairs(sk.skillmorphs) do
      morphdefs[morph] = table.size(t)
    end

    for _, t in pairs(sk.morphsforskill) do
      table.sort(t, function(a,b) if morphdefs[a] and morphdefs[b] then return morphdefs[a] > morphdefs[b] end end)
    end
  end
end
  signals.morphskillchanged:connect(sk.updatemorphskill, "update available morph skills")
  signals.systemstart:connect(sk.updatemorphskill, "update available morph skills")
end

signals.gmcpcharitemslist:connect(function ()
  if not gmcp.Char.Items.List.location then svo.debugf("(GMCP problem) location field is missing from Achaea's response.") return end
  if not sk.inring or gmcp.Char.Items.List.location ~= 'inv' then return end

  svo.sendc("inr all", true)

  sk.inring = nil
  svo.echof("Stuffing everything away...")
end, "handle inr")

signals.gmcpcharitemslist:connect(function()
  if not sk.retrieving_herbs or gmcp.Char.Items.List.location ~= 'room' then return end

  for _, t in pairs(gmcp.Char.Items.List.items) do
    if rift.herbs_singular[t.name] then
      svo.doaddfree("get "..t.id)
    end

    -- tally up rift.herbs_plural items
    for _,l in pairs(rift.herbs_plural) do
      local result = t.name:match(l)
      if result then
        for _ = 1, tonumber(result) do -- getting group # only gets 1 item, have to repeatedly cycle it
          svo.doaddfree("get "..t.id)
        end
      end
    end
  end

  sk.retrieving_herbs = nil
end, "pick up plants")

for _, herb in ipairs{'elm', 'valerian', 'skullcap'} do
  sk[herb..'_smokepuff'] = function ()
    if not conf.arena then
      pipes[herb].puffs = pipes[herb].puffs - 1
      if pipes[herb].puffs < 0 then pipes[herb].puffs = 0 end
    end

    if herb == 'valerian' then
      signals.after_lifevision_processing:unblock(cnrl.checkwarning)
    end

    moveCursor(0, getLineNumber()-1)
    moveCursor(#getCurrentLine(), getLineNumber())
    setFgColor(unpack(svo.getDefaultColorNums))
    insertText(string.format(" (%s %s left)", pipes[herb].puffs, pipes[herb].filledwith))
    resetFormat()
    moveCursorEnd()
  end
end

if svo.haveskillset('occultism') then
signals.gmcpcharitemslist:connect(function ()
  if not gmcp.Char.Items.List.location or not gmcp.Char.Items.List.items then svo.debugf("(GMCP problem) location or items field is missing from Achaea's response.") return end

  if gmcp.Char.Items.List.location ~= 'inv' then return end

  for _, t in pairs(gmcp.Char.Items.List.items) do
    if t.name then
      if t.name == "a heartstone" then
        defences.got('heartstone')
      elseif t.name == "a simulacrum shaped like "..me.name then
        defences.got('simulacrum')
      end
    end
  end
end, "check heartstoneand simulacrum defs")
end

function svo.sk.enable_single_prompt()
  if svo.bottomprompt then svo.bottomprompt:show() end
  svo.bottomprompt = Geyser.MiniConsole:new({
    name="svo.bottomprompt",
    x=0, y="100%",
    width="98%", height='1c',
    fontSize = conf.singlepromptsize or 11
  })
  svo.bottomprompt:setFontSize(conf.singlepromptsize or 11)

  function svo.bottomprompt:reposition()
     local _,height = calcFontSize(conf.singlepromptsize or 11)

     if not svo.bottom_border or svo.bottom_border ~= height then
       svo.bottom_border = height
       tempTimer(0, function() setBorderBottom(height) end)
     end

     moveWindow(self.name, self:get_x(), self:get_y()-(height+(height/3)))
     resizeWindow(self.name, self:get_width(), self:get_height())
  end
  setBackgroundColor("svo.bottomprompt",0,0,0,255)
  svo.bottomprompt:reposition()

  if svo.moveprompt then killTrigger(svo.moveprompt) end
  -- moveprompt = tempRegexTrigger('^', [[
  --   if not isPrompt() then return end
  --   selectCurrentLine()
  --   copy()
  --   svo.bottomprompt:clear()
  --   svo.bottomprompt:paste()
  --   if svo.conf.singlepromptblank then
  --     replace("")
  --   elseif not svo.conf.singlepromptkeep then deleteLine() end
  --   deselect()
  -- ]])
end

function svo.sk.showstatchanges()
  if svo.logging_in then return end
  local t = sk.statchanges
  if #t > 0 then
    if conf.singleprompt then
      moveCursor(0, getLineNumber()-1)
      moveCursor(#getCurrentLine(), getLineNumber())
      dinsertText(' <192,192,192>('..table.concat(t, ", ")..'<192,192,192>) ')
    else
      decho('<192,192,192>('..table.concat(t, ", ")..'<192,192,192>) ')
    end

    resetFormat()
    if conf.singleprompt then moveCursorEnd() end
  end
end

-- logic: if something we are wielding does not show up unparryables, then we can wield
function svo.sk.have_parryable()
  me.unparryables = me.unparryables or {}

  for _, item in pairs(me.wielded) do
    if not me.unparryables[item.name] then return true end
  end
end

function svo.sk.cant_parry()
  local t = {}
  me.unparryables = me.unparryables or {}
  for _, item in pairs(me.wielded) do
    if not me.unparryables[item.name] then
      t[#t+1] = item.name
      me.unparryables[item.name] = true
    end
  end

  if #t > 0 then
    echo'\n'

    local lines = {
      "Oh, looks like we can't parry with %s.",
      "Doesn't look like we can parry with %s.",
      "Oops. Can't parry with %s.",
      "And %s won't fly, either.",
      "And %s won't work, either."
    }

    svo.echof(lines[math.random(#lines)], table.concat(t, ' or '))
  end
end

signals.newroom:connect(function ()
  -- don't get tricked by dementia, which does send false gmcp
  -- nor by hidden dementia
  if affs.dementia or affs.unknownany or affs.unknownmental or not conf.autoarena then return end

  local t = sk.arena_areas

  local area = atcp.RoomArea or gmcp.Room.Info.area

  if t[area] and not conf.arena then
    conf.arena = true
    raiseEvent("svo config changed", 'arena')
    svo.prompttrigger("arena echo", function()
      local echos = {"Arena mode enabled. Good luck!", "Beat 'em up! Arena mode enabled.", "Arena mode on.", "Arena mode enabled. Kill them all!"}
      svo.itf(echos[math.random(#echos)]..'\n')
    end)
  elseif conf.arena and not t[area] then
    conf.arena = false
    raiseEvent("svo config changed", "arena`")
    tempTimer(0, function()
      local echos = {"Arena mode disabled."}
      svo.echof(echos[math.random(#echos)]..'\n')

      -- the game resets armbals quietly
      if not bals.rightarm then bals.rightarm = true end
      if not bals.leftarm then bals.leftarm = true end
    end)
  end
end, 'update arena status')

-- this will get connected on load
sk.check_burrow_pause = function()
  local roomname = _G.gmcp.Room.Info.name

  if not conf.paused and roomname == "Surrounded by dirt" then sk.paused_for_burrow = true; svo.app('on')
  elseif sk.paused_for_burrow and conf.paused and roomname ~= "Surrounded by dirt" and stats.currenthealth > 0 then svo.app('off')
  end
end

function svo.sk.check_shipmode()
  -- failsafe for disabling captain control - since there are a few ways in which you can lose it without an explicit line.
  if conf.shipmode and gmcp.Room.Info.environment ~= 'Vessel' then
    svo.config.set('shipmode', 'off', true)
  end
end

function svo.balanceful_used()
  return (sys.balanceid == sys.balancetick) and true or false
end

-- getNetworkLatency, with a cap
function svo.getping(caparg)
  local cap = caparg or .500
  local lat = getNetworkLatency()

  return (lat < cap) and lat or cap
end

-- returns true if a curing command was seen in this paragraph
function svo.sk.sawcuring()
  -- don't search the buffer, but set a flag, because people could be gagging the line and buffer search will thus fail
  return sk.sawcuringcommand and true or false
end

function svo.sk.sawqueueing()
  return sk.sawqueueingcommand and true or false
end

function svo.amiwielding(what)
  for _, item in pairs(svo.me.wielded) do
    if item.name:find("%f[%a]"..what.."%f[%A]") then return true end
  end

  return false
end

function sk.sendqueuecmd(...)
  local args = {...}
  for i = 1, #args do
    local what = args[i]
    if type(what) == 'string' then
      -- flush the buffer if it'll overflow how many chars we can send
      if sk.sendqueuel + #what + 1 >= sk.achaea_command_max_length then
        sk.dosendqueue()
      end

      sk.sendqueue[#sk.sendqueue+1] = what
      sk.sendqueuel = sk.sendqueuel + #what + 1 -- +1 for the separator
      if not sk.sendcuringtimer then
        sk.sendcuringtimer = tempTimer(0, sk.dosendqueue)
      end
    elseif type(what) == 'table' and what.func then
      sk.dosendqueue() --flush send queue first
      if what.args then
        what.func(unpack(what.args))
      else
        what.func()
      end
    end
  end
end

function svo.sendcuring(what)
  what = "curing "..what

  sk.sendqueuecmd(what)
end

function sk.dosendprios()
    local sys = svo.sys
    local function resetpriolength()
      sk.priolength = {affqueue = 0, affqueueslow = 0, defqueue = 0, defqueueslow = 0}
    end
    local function sendstuff(what, queue)
      if queue == "normal" then
        send(what,false)
      elseif queue == "slow" then
        if sys.sync == true then
          sendAll("curingset switch slowcuring", what, false)
        else
          sendAll("curingset switch slowcuring", what, "curingset switch normal", false)
        end
      end
    end
    
    if sk.sendpriotimer then killTimer( sk.sendpriotimer ) end
  
    if #sk.affqueue > 0 then
        local what = "curing priority"..table.concat(sk.affqueue)
        if sk.queuecount > 4 then
          tempTimer(1,function()
            sendstuff(what,"normal")
            sk.affqueue = {}
            sk.queuecount = 0
          end)
        else
          sendstuff(what,"normal")
          sk.affqueue = {}
          sk.queuecount = sk.queuecount + 1
        end
    end
    if #sk.defqueue > 0 then
        local what = "curing priority defence"..table.concat(sk.defqueue)
        if sk.queuecount > 4 then
          tempTimer(1,function()
          sendstuff(what,"normal")
          sk.defqueue = {}
          sk.queuecount = 0
          end)
        else
          sendstuff(what,"normal")
          sk.defqueue = {}
          sk.queuecount = sk.queuecount + 1
        end
    end
    if #sk.affqueueslow > 0 then
        local what = "curing priority"..table.concat(sk.affqueueslow)
        if sk.queuecount > 4 then
          tempTimer(1,function()
          sendstuff(what,"slow")
          sk.affqueueslow = {}
          sk.queuecount = 0
          end)
        else
          sendstuff(what,"slow")
          sk.affqueueslow = {}
          sk.queuecount = sk.queuecount + 1
        end
    end
    if #sk.defqueueslow > 0 then
        local what = "curing priority defence"..table.concat(sk.defqueueslow)
        if sk.queuecount > 4 then
          tempTimer(1,function()
          sendstuff(what,"slow")
          sk.defqueueslow = {}
          sk.queuecount = 0
          end)
        else
          sendstuff(what,"slow")
          sk.defqueueslow = {}
          sk.queuecount = sk.queuecount + 1
        end
    end
  tempTimer(1,function() sk.queuecount = 0 end)     
  sk.sendpriotimer = nil
  resetpriolength()
end

function svo.sendcuringprio(prio, kind, slow)
  local sys = svo.sys
  prio = " "..prio
  local what, tab, cmd
  if kind == 'affliction' then
    tab = "affqueue"
    cmd = "curing priority"
  elseif kind == 'defence' then
    tab = "defqueue"
    cmd = "curing priority defence"
  else
    return
  end
  if slow == true then tab = tab.."slow" end
  if (sk.priolength[tab] + #cmd + #prio) >= sk.achaea_command_max_length then
    what = cmd..table.concat(sk[tab])
    if slow == true then
      if sys.sync then
        sendAll( "curingset switch slowcuring", what, false )
      else
        sendAll( "curingset switch slowcuring", what, "curingset switch normal", false )
      end
    else
      send( what )
    end
    sk[tab] = {}
  end
  if not table.contains(sk[tab], prio) then 
    sk[tab][#sk[tab]+1] = prio
    sk.priolength[tab] = sk.priolength[tab] + #prio
  end
  if not sk.sendpriotimer then
    sk.sendpriotimer = tempTimer(0, sk.dosendprios)
  end
end

-- public function
svo.sendc = sk.sendqueuecmd

function sk.dosendqueue()
  if svo.logging_in then return end
  if sk.sendcuringtimer then killTimer(sk.sendcuringtimer) end

  if #sk.sendqueue <= 1 then
    send(sk.sendqueue[1] or '', false)
  elseif conf.commandseparator and conf.commandseparator ~= '' and #sk.sendqueue <= 10 then
    send(table.concat(sk.sendqueue, conf.commandseparator), false)
  elseif #sk.sendqueue <= 9 then
    send("9multicmd {"..table.concat(sk.sendqueue, "}{").."}", false)
  else
    local text = "" -- let's have this defined up here for reuse below
    if #sk.sendqueue > 10 then -- oops, we have lots of commands to send
      local send_queue = {} -- here be our collector/grabber thing
      for _, item in ipairs(sk.sendqueue) do -- let's iterate through!
        table.insert(send_queue, item)
        if #send_queue == 10 then -- yay we're ready to send this batch!
          text = table.concat(send_queue, "/")
          sendAll("setalias multicmd "..text, "multicmd", false)
          send_queue = {}
        end
      end
      if #send_queue > 0 then -- do we still have commands to send?
        text = table.concat(send_queue, "/")
        sendAll("setalias multicmd "..text, "multicmd", false)
      end
    else -- this is likely a no-op now but I didn't want to mess with it
      text = table.concat(sk.sendqueue, "/")
      sendAll("setalias multicmd "..text, "multicmd", false)
    end
  end

  sk.sendqueue = {}
  sk.sendqueuel = 18 -- 'setalias multicmd ' is 24 characters
  sk.sendcuringtimer = nil
end

function svo.sk.setup9multicmd()
  send("setalias 9multicmd %1/%2/%3/%4/%5/%6/%7/%8/%9", false)
end
signals.charname:connect(sk.setup9multicmd, 'setup 9multicmd')
signals.gmcpcharname:connect(sk.setup9multicmd, 'setup 9multicmd')

svo['9multicmd_cleared'] = function()
  send("setalias 9multicmd %1/%2/%3/%4/%5/%6/%7/%8/%9")

  echo("\n")
  svo.echof("Oy! I need that! This is for vconfig batch to work.")

  svo.reenabled9multi = tempTimer(5, function() svo.reenabled9multi = nil end)
end

-- things line blind/deaf can be either afflictions or defences.
-- This function is called whenever their status as a defence might change, and you have them - hence they need to be
-- changed to a defence now or back
function svo.sk.fix_affs_and_defs()
  if affs.blindaff and ((defdefup[defs.mode].blind) or (conf.keepup and defkeepup[defs.mode].blind)
    or (svo.me.class ~= 'Apostate' and defc.mindseye)) then
    svo.rmaff('blindaff')
    defences.got('blind')
    svo.echof("blindness is now considered a defence.")
  elseif defc.blind and not ((defdefup[defs.mode].blind) or (conf.keepup and defkeepup[defs.mode].blind)
   or (svo.me.class ~= 'Apostate' and defc.mindseye)) then
    defences.lost('blind')
    svo.addaffdict(svo.dict.blindaff)
    svo.echof("blindness is now considered an affliction, will cure it.")
  end

  if affs.deafaff and ((defdefup[defs.mode].deaf) or (conf.keepup and defkeepup[defs.mode].deaf) or defc.mindseye) then
    svo.rmaff('deafaff')
    defences.got('deaf')
    svo.echof("deafness is now considered a defence.")
  elseif defc.deaf and not ((defdefup[defs.mode].deaf) or (conf.keepup and defkeepup[defs.mode].deaf) or defc.mindseye) then
    defences.lost('deaf')
    svo.addaffdict(svo.dict.deafaff)
    svo.echof("deafness is now considered an affliction, will cure it.")
  end
  
    --ablaze check for fire elementals, they always must be ON FIRE!
  if affs.ablaze and ((defdefup[defs.mode].torch) or (conf.keepup and defkeepup[defs.mode].torch)
   and (svo.me.class == "fire Elemental Lord" or svo.me.class == "fire Elemental Lady")) then
    svo.rmaff('ablaze')
    defences.got('torch')
    svo.echof("ablaze is now considered a defence.")
  elseif defc.torch and ((svo.me.class ~= "fire Elemental Lord" and svo.me.class ~= "fire Elemental Lady") or
   not ((defdefup[defs.mode].torch) or (conf.keepup and defkeepup[defs.mode].torch))) then
    defences.lost('torch')
    svo.addaffdict(svo.dict.ablaze)
    svo.echof("ablaze is now considered an affliction, will cure it.")
  end
    svo.sk.updateserversideprios()
end


function svo.sk.checkrewield()
  local s,m = pcall(function()
    if svo.paragraph_length > 1 and not svo.find_until_last_paragraph("You cease to prop up a tall totem pole.", 'exact') and
      not svo.find_until_last_paragraph("You lob", 'substring') and not svo.lifevision.l.breath_gone and
      not svo.find_until_last_paragraph("You begin to wield", 'substring') and
      not svo.find_until_last_paragraph("You start to wield", 'substring') and
      sk.rewielddables then
      -- we wish to rewield wieldables!
      svo.dict.rewield.rewieldables = deepcopy(sk.rewielddables)
      svo.debugf("dict.rewield.rewieldables - %s", svo.pl.pretty.write(svo.dict.rewield.rewieldables))
      svo.echof("Need to rewield %s%s!", tostring(svo.dict.rewield.rewieldables[1].name),
        tostring(((svo.dict.rewield.rewieldables[2] and svo.dict.rewield.rewieldables[2].name) and
          (" and "..svo.dict.rewield.rewieldables[2].name) or "")))
    end
  end)
  if not s then
    echoLink("(e!)", [[echo([=[The problem was: ']] .. tostring(m) .. [[']=])]], 'Oy - there was a problem. Click on this link '
         ..'and submit a bug report with what it says along with a copy/paste of what you saw.')
  end

  sk.rewielddables = nil
  signals.before_prompt_processing:disconnect(sk.checkrewield)
end

signals.gmcpcharitemsremove:connect(function ()
  sk.removed_something = true
  sk.onprompt_beforeaction_add('gmcpcharitemsremove', function ()
    sk.removed_something = nil
  end)
end, 'update sk.removed_something')

end -- end of svo skeleton loader

if svo.systemloaded then svo.loader.sk() end
  ```
  
</details>


  <summary>Modified Controllers</summary>
  
  ```lua
  svo = svo or {}; svo.loader = svo.loader or {}
svo.loader.controllers = function()

local sys, affs, defdefup, defkeepup, signals = svo.sys, svo.affs, svo.defdefup, svo.defkeepup, svo.signals
local conf, sk, me, defs, defc = svo.conf, svo.sk, svo.me, svo.defs, svo.defc
local defences, stats, cnrl, rift = svo.defences, svo.stats, svo.cnrl, svo.rift
local bals, pipes, valid, actions = svo.bals, svo.pipes, svo.valid, svo.actions
local lifevision = svo.lifevision

local oldhealth, oldmana = 0, 0
local oldmaxhealth, oldmaxmana, oldmaxendurance, oldmaxwillpower = 0, 0, 0, 0
me.healthchange, me.manachange = 0, 0
local function calculatestatchanges()
  local t = {}
  
  me.healthchange = 0
  me.manachange = 0
	if affs.blackout then return end
  if oldhealth > stats.currenthealth then
    me.healthchange = stats.currenthealth - oldhealth

    if conf.showchanges then
      if conf.changestype == 'full' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128> Health", me.healthchange)
      elseif conf.changestype == 'short' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128>h", me.healthchange)
      elseif conf.changestype == 'fullpercent' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128> Health, %.1f%%", me.healthchange, 100/stats.maxhealth*me.healthchange*-1)
      elseif conf.changestype == 'shortpercent' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128>h, %.1f%%", me.healthchange, 100/stats.maxhealth*me.healthchange*-1)
      end
    end

  elseif oldhealth < stats.currenthealth then
    me.healthchange = stats.currenthealth - oldhealth

    if conf.showchanges then
      if conf.changestype == 'full' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128> Health", me.healthchange)
      elseif conf.changestype == 'short' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128>h", me.healthchange)
      elseif conf.changestype == 'fullpercent' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128> Health, %.1f%%", me.healthchange, 100/stats.maxhealth*me.healthchange)
      elseif conf.changestype == 'shortpercent' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128>h, %.1f%%", me.healthchange, 100/stats.maxhealth*me.healthchange)
      end
    end
  end

  if oldmana > stats.currentmana then
    me.manachange = stats.currentmana - oldmana

    if conf.showchanges then
      if conf.changestype == 'full' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128> Mana", me.manachange)
      elseif conf.changestype == 'short' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128>m", me.manachange)
      elseif conf.changestype == 'fullpercent' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128> Mana, %.1f%%", me.manachange, 100/stats.maxmana*me.manachange*-1)
      elseif conf.changestype == 'shortpercent' then
        t[#t+1] = string.format("<255,0,0>%d<128,128,128>m, %.1f%%", me.manachange, 100/stats.maxmana*me.manachange*-1)
      end
    end

  elseif oldmana < stats.currentmana then
    me.manachange = stats.currentmana - oldmana

    if conf.showchanges then
      if conf.changestype == 'full' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128> Mana", me.manachange)
      elseif conf.changestype == 'short' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128>m", me.manachange)
      elseif conf.changestype == 'fullpercent' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128> Mana, %.1f%%", me.manachange, 100/stats.maxmana*me.manachange)
      elseif conf.changestype == 'shortpercent' then
        t[#t+1] = string.format("<0,255,0>+%d<128,128,128>m, %.1f%%", me.manachange, 100/stats.maxmana*me.manachange)
      end
    end
  end

  -- update the public old values
  me.oldhealth, me.oldmana = oldhealth, oldmana

  -- update oldhealth, oldmana to current values
  oldhealth, oldmana = stats.currenthealth, stats.currentmana
  oldmaxhealth, oldmaxmana = stats.maxhealth, stats.maxmana
  oldmaxendurance, oldmaxwillpower= stats.maxendurance, stats.maxwillpower
	
  -- store away changed for showing later, as the custom prompt that follows overrides
  sk.statchanges = t
end

local blackout_flag
function svo.blackout()
  blackout_flag = true
end

local function checkblackout()
  if blackout_flag and not affs.blackout then
    valid.simpleblackout()
  elseif not blackout_flag and affs.blackout and getCurrentLine() ~= "" then
    if actions.touchtree_misc then
      lifevision.add(actions.touchtree_misc.p, nil, 'blackout')
    else
      svo.checkaction(svo.dict.blackout.waitingfor, true)
      lifevision.add(actions.blackout_waitingfor.p)
    end
  end

  blackout_flag = false
end

function svo.valid.setup_prompt()
  if line == "-" or line:find("^%-%d+%-$") or line == " Vote-" then
    -- bals.balance = true
    -- bals.equilibrium = true
    bals.rightarm = 'unset'
    bals.leftarm = 'unset'
  else
    bals.balance = false
    bals.equilibrium = false
    svo.pflags = {}
  end
end

local function check_promptflags()
  local pflags = svo.pflags

  if pflags.b and not defc.blind and not affs.blindaff then
    if ((defdefup[defs.mode].blind) or (conf.keepup and defkeepup[defs.mode].blind)) then
      if svo.me.class == 'Apostate' and not defc.mindseye then
        return
      end
      defences.got('blind')
    else
      svo.addaffdict(svo.dict.blindaff)
    end
  elseif not pflags.b and (defc.blind or affs.blindaff) then
    svo.rmaff('blindaff')
    defences.lost('blind')
  end

  if pflags.d and not defc.deaf and not affs.deafaff then
    if ((defdefup[defs.mode].deaf) or (conf.keepup and defkeepup[defs.mode].deaf) or defc.mindseye) then
      defences.got('deaf')
    else
      svo.addaffdict(svo.dict.deafaff)
    end
  elseif not pflags.d and (defc.deaf or affs.deafaff) then
    svo.rmaff('deafaff')
    defences.lost('deaf')
  end

  if pflags.k and not defc.kola then
    defences.got('kola')
  elseif not pflags.k and defc.kola then
    defences.lost('kola')
  end

  if pflags.c and not defc.cloak then
    defences.got('cloak')
  elseif not pflags.c and defc.cloak then
    defences.lost('cloak')
  end

if svo.haveskillset('shindo') then
  stats.shin = line:match("%-(%d+)%-") or line:match("%-(%d+) Vote%-") or 0
end

if svo.haveskillset('kaido') then
  stats.kai = line:match("%-(%d+)%-") or line:match("%-(%d+) Vote%-") or 0
end

if svo.haveskillset('necromancy') then
  if pflags.at then defences.got('blackwind') else defences.lost('blackwind') end
end

if svo.haveskillset('occultism') then
  if pflags.at then defences.got('astralform') else defences.lost('astralform') end
end

if svo.haveskillset('subterfuge') then
  if pflags.at then defences.got('phase') else defences.lost('phase') end
end

  local oldgametarget, oldgametargethp = me.gametarget, me.gametargethp
  me.gametarget, me.gametargethp = line:match("%[(.-)%](%d+)%%")
  if me.gametargethp then me.gametargethp = tonumber(me.gametargethp) end
  if oldgametarget ~= me.gametarget then
    raiseEvent("svo gametarget changed", me.gametarget)
  end
  if oldgametargethp ~= me.gametargethp then
    raiseEvent("svo gametargethp changed", me.gametarget, me.gametargethp)
  end

if svo.haveskillset('weaponmastery') then
  stats.weaponmastery = line:match("k(%d+)")
end

  me.servertime = line:match("-s(%d+:%d+:%d+%.%d+)")
end

-- This the order that events things happen on the prompt function:
-- 
--     \
--      |onprompt
--      |signals.before_prompt_processing
--      |  \
--      |   |prompt_stats (stores new prompt stats)
--      |   |sk.acrobatics_pronecheckf (toggled)
--      |   |valid.check_life
--      |   |sk.onprompt_beforelifevision_do (-> svo.aiprompt())
--      |
--      |send_in_the_gnomes
--      |  \
--      |   |lifevision.validate
--      |   |signals.after_lifevision_processing
--      |      \
--      |       |sk.onprompt_beforeaction_do / prompttrigger
--      |       |cnrl.checkwarning
--      |       |sp_checksp
--      |   |make_gnomes_work (curing commands)
--      |
--      |signals.after_prompt_processing
--      |  \
--      |   |custom prompt
--      |   |cnrl.dolockwarning

function svo.onprompt()
  raiseEvent("svo before the prompt")
  sk.processing_prompt = true
  sk.systemscommands = {}

  svo.promptcount = svo.promptcount + 1

  checkblackout()
  check_promptflags()

  sys.lagcount = 0
  svo.prompt_stats()
  calculatestatchanges()

  local s,m = pcall(signals.before_prompt_processing.emit, signals.before_prompt_processing)
  if not s then
    echoLink("(e!)", [[svo.echof([=[The problem was: stuff before the actual work failed (]]..tostring(m)..[[)]=])]], 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a copy/paste of what you saw.')
  end

  svo.send_in_the_gnomes()

  if conf.showchanges and not conf.commandecho or (conf.commandecho and conf.commandechotype == 'fancynewline') then
    sk.showstatchanges()
  end

  local processing_status, processing_message = pcall(signals.after_prompt_processing.emit, signals.after_prompt_processing)
  if not processing_status then
    svo.debugf(processing_message)
    echoLink("(e!)", [[svo.echof([=[The problem was: stuff after the actual work failed (]]..processing_message..[[)]=])]], 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a copy/paste of what you saw.')
  end

  if conf.showchanges and conf.commandecho and conf.commandechotype ~= 'fancynewline' then sk.showstatchanges() end
  if sys.deffing then svo.defupfinish() end

  -- show system commands
  svo.fancysendall()

  -- send off all batched commands
  sk.dosendqueue()

  local currentlinenumber = getLastLineNumber('main')
  svo.lastpromptnumber = currentlinenumber
  -- record the time of the latest prompt we've seen - doing so is okay because we aren't using the current time, but retrieving the already calculated time from Mudlet
  -- using this, we can then find which was the most recent prompt line. Sometimes another line will share the time with the prompt, but the prompt will always be latest
  svo.lastprompttime = getTimestamp(currentlinenumber)
  svo.paragraph_length = 0
  sk.processing_prompt = false
  raiseEvent("svo done with prompt")
end

signals.after_prompt_processing:connect(function ()
  -- svo prompt pipeline - deals with the custom and singleprompts. This is done before onprompt()

  -- move the real prompt over for later use.
  moveCursorEnd()
  if selectString( getCurrentLine(), 1 ) ~= -1 then
    copy()
    moveCursorEnd('svo_prompt')
    paste('svo_prompt')
  end

  -- stats are updated in a pre-emit of before_prompt_processing; available to the customprompt here
  if affs.blackout or svo.innews then return end

  -- replace w/ customprompt if necessary
  if conf.customprompt then
    selectString(line, 1)
    replace("")
  end

  -- prefix an orange '?:' if we don't know the exact stats
  if affs.recklessness or affs.blackout then
    local currentline = getLineCount()
    moveCursor('main', 0, currentline)
    deselect()
    setFgColor(255, 102, 0)
    insertText("?:")
    deselect(); resetFormat()
    moveCursorEnd()
  end

  if sys.sync then
    local currentline = getLineCount()
    deselect()
    moveCursor('main', 0, currentline)
    setFgColor(255, 0, 0)
    insertText("(")
    moveCursor('main', 1, currentline)

    -- you're overriding the system, green
    if svo.sacid then
      setFgColor(0,255,0)
    -- system is doing something, red
    elseif sk.doingstuff_inslowmode() then
      setFgColor(255,0,0)
    -- system isn't doing anything and you aren't overriding, blue
    else
      setFgColor(0,0,255)
    end

    if svo.sacid then
      insertLink('a', 'svo.echof[[You\'re currently overriding the system]]', 'You were overriding the system at this point', true)
    elseif sk.syncdebug then
      insertLink('a', 'svo.echof[['..sk.syncdebug..']]', 'Click to see actions we were considering doing at this point', true)
    else
      insertText('a')
    end

    moveCursor('main', 2, currentline)
    setFgColor(255, 0, 0)
    insertText(")")
    moveCursor('main', 3, currentline)
    setFgColor(0,0,0)
    insertText(" ")
    moveCursorEnd()
    resetFormat()
  end

  if conf.paused then
    moveCursor('main', 0, getLineCount())
    cinsertText("<a_red>(<a_darkgrey>p<a_red>)<black> ")
  end

  if conf.customprompt then
    cecho(svo.cp.display() or "")
  end

  -- then do singleprompt business
  if conf.singleprompt then
    selectString(getCurrentLine(), 1)
    copy()
    svo.bottomprompt:clear()
    svo.bottomprompt:paste()

    if conf.singlepromptblank then
      replace("")
    elseif not conf.singlepromptkeep then
      deleteLine()
    end

    deselect()
  end
end, "main prompt processing")

signals.gmcpcharname:connect(function()
  svo.innews = nil
  sk.logged_in = true
end, "set login status")

signals.gmcpcharstatus:connect(function()
  sys.charname = gmcp.Char.Status.name
  me.name = gmcp.Char.Status.name
end, "grab gmcp name")

local old500num = 0
local old500p = false


function svo.prio_makefirst(action, balance)
  svo.assert(action and svo.dict[action], "svo.prio_makefirst: " .. (action and action or 'nil') .. " isn't a valid action.")

  local act = svo.dict[action]

  -- find if it's only one available
  if not balance then
    local count = table.size(act)
    if act.aff then count = count - 1 end
    if act.waitingfor then count = count - 1 end

    svo.assert(count == 1, "svo.prio_makefirst: " .. action .. " uses more than one balance, which one do you want to move?")
    local balance = false
    for k,_ in pairs(act) do
      if k ~= 'aff' and k ~= 'waitingfor' then balance = k end
    end
  end

  svo.assert(act[balance] and act[balance] ~= 'aff' and act[balance] ~= 'waitingfor', "svo.prio_makefirst: " .. action .. " doesn't use the " .. (balance and balance or 'nil') .. " balance.")

  local beforestate = sk.getbeforestateprios()

  -- at this point, we both have the act and balance we want to move up.
  -- logic: move to 500, remember the original val. when we have to move back,
  -- we'll swap it to the original val.
  svo.prio_undofirst()

  old500num = act[balance].spriority
  old500p = act[balance]
  act[balance].spriority = 500

  local afterstate = sk.getafterstateprios()
  sk.notifypriodiffs(beforestate, afterstate)
end

function svo.prio_undofirst()
  if not old500p then return end

  local beforestate = sk.getbeforestateprios()

  old500p.spriority = old500num
  old500p, old500num = false, nil

  local afterstate = sk.getafterstateprios()
  sk.notifypriodiffs(beforestate, afterstate)
end

function svo.prio_slowswap(what, arg3, echoback, callback, ...)
  local sendf; if echoback then sendf = svo.echof else sendf = svo.errorf end
  local what, balance = what:match("(%w+)_(%w+)")
  local balance2
  if not tonumber(arg3) then
    svo.assert(balance and balance2, "What balances do you want to use for swapping?", sendf)
    arg3, balance2 = arg3:match("(%w+)_(%w+)")
  end

  local beforestate = sk.getbeforestateprios()

  if tonumber(arg3) then -- swap to a #
    local name, balance2 = svo.prio.getslowaction(tonumber(arg3))
    if not name then -- see if we have anyone in that # already
      svo.dict[what][balance].spriority = arg3
      if echoback then
        svo.echof("%s is now at %d.", what, arg3)
      end
    else -- if we do have someone at that #, swap them
      svo.dict[what][balance].spriority, svo.dict[name][balance2].spriority =
      svo.dict[name][balance2].spriority, svo.dict[what][balance].spriority
      if echoback then svo.echof("%s is now > %s.", what, name) end
      if echoback then svo.echof("<0,255,0>%s (%s) <255,255,255>> <0,255,0>%s (%s)", what, balance, name, balance2) end
    end
  else -- swap one action_balance with another action_balance
    if svo.dict[what][balance].spriority < svo.dict[arg3][balance2].spriority then
      svo.dict[what][balance].spriority, svo.dict[arg3][balance2].spriority =
      svo.dict[arg3][balance2].spriority, svo.dict[what][balance].spriority
      if echoback then svo.echof("%s is now > %s.", what, arg3) end
      if echoback then svo.echof("<0,255,0>%s (%s) <255,255,255>> <0,255,0>%s (%s)", arg3, balance2, what, balance) end
    elseif echoback then
      svo.echof("%s is already > %s.", what, arg3)
    end
  end

  local afterstate = sk.getafterstateprios()
  sk.notifypriodiffs(beforestate, afterstate)

  if callback and type(callback) == 'function' then callback(...) end
end

function svo.prio_swap(what, balance, arg2, arg3, echoback, callback, ...)
  local sendf; if echoback then sendf = svo.echof else sendf = svo.errorf end
  svo.assert(what and svo.dict[what] and balance and svo.dict[what][balance] and balance ~= 'aff' and balance ~= 'waitingfor', "what item and balance do you want to swap?", sendf)

  local function swaptwo(what, name, balance, ...)
    if svo.dict[what][balance].aspriority < svo.dict[name][balance].aspriority then
      svo.dict[what][balance].aspriority, svo.dict[name][balance].aspriority =
      svo.dict[name][balance].aspriority, svo.dict[what][balance].aspriority
      if echoback then svo.echof("<0,255,0>%s <255,255,255>> <0,255,0>%s%s in %s balance", what, name, svo.getDefaultColor(), balance) end
    elseif svo.dict[what][balance].aspriority > svo.dict[name][balance].aspriority then
      svo.dict[what][balance].aspriority, svo.dict[name][balance].aspriority =
      svo.dict[name][balance].aspriority, svo.dict[what][balance].aspriority
      if echoback then svo.echof("<0,255,0>%s <255,255,255>> <0,255,0>%s%s in %s balance", name, what, svo.getDefaultColor(), balance) end
    end

    if callback and type(callback) == 'function' then callback(...) end
  end

  local beforestate = sk.getbeforestateprios()

  -- we want our 'what' to be at this arg2 number, swap what was there with its previous position
  if not arg3 then

    svo.assert(tonumber(arg2), "what number do you want to swap " .. what .. " with?", sendf)
    local to_num = tonumber(arg2)
    local name = svo.prio.getaction(to_num, balance)

    -- swapping two affs
    if name then
      swaptwo(what, name, balance, ...)

    -- or just setting one aff
    else
      svo.dict[what][balance].aspriority = to_num
      if echoback then
        svo.echof("%s is now at %d.", what, to_num)
      end
    end

    local afterstate = sk.getafterstateprios()
    sk.notifypriodiffs(beforestate, afterstate)

    return
  end

  -- we want to swap two affs
  svo.assert(svo.dict[arg2] and svo.dict[arg2][arg3], "what balance of "..arg2.." do you want to swap with?", sendf)
  swaptwo(what, arg2, arg3, ...)

  local afterstate = sk.getafterstateprios()
  sk.notifypriodiffs(beforestate, afterstate)
end

svo.prompt_stats = function ()
  local s,m = pcall(function()
    if not (gmcp and gmcp.Char and gmcp.Char.Vitals) then
        if not conf.paused then
          conf.paused = true
          echo"\n" svo.echof("Paused the system - please enable GMCP for it in Mudlet settings!") svo.showprompt()
          raiseEvent("svo config changed", 'paused')
        end
      return
    end

    local temp = {
      maxhealth = stats.maxhealth or 0,
      maxmana = stats.maxmana or 0,
    }

    local vitals = gmcp.Char.Vitals
    local sformat = string.format


    stats.currenthealth, stats.maxhealth,
    stats.currentmana, stats.maxmana,
    stats.currentendurance, stats.maxendurance,
    stats.currentwillpower, stats.maxwillpower
     =
        vitals.hp, vitals.maxhp,
        vitals.mp, vitals.maxmp,
        vitals.ep, vitals.maxep,
        vitals.wp, vitals.maxwp

    stats.nextlevel = gmcp.Char.Vitals.nl or 0
    stats.xprank = gmcp.Char.Status.xprank or 0

--Sets stats due to gmcp values being nil in blackout
    stats.currenthealth            = stats.currenthealth or 1
    stats.currentmana              = stats.currentmana or 1
    stats.currentendurance         = stats.currentendurance or 4000
    stats.currentwillpower         = stats.currentwillpower or 4000
    stats.maxhealth                = stats.maxhealth or oldmaxhealth
    stats.maxmana                  = stats.maxmana or oldmaxmana
    stats.maxendurance             = stats.maxendurance or oldmaxendurance
    stats.maxwillpower             = stats.maxwillpower or oldmaxwillpower

    stats.hp = sformat("%.1f", (100/stats.maxhealth)*stats.currenthealth)
    stats.mp = sformat("%.1f", (100/stats.maxmana)*stats.currentmana)
    stats.wp = sformat("%.1f", (100/stats.maxwillpower)*stats.currentwillpower)
    stats.ed = sformat("%.1f", (100/stats.maxendurance)*stats.currentendurance)
		
    for i,j in pairs(stats) do
      stats[i] = tonumber(j) or 0
    end

    if (stats.currentwillpower <= 1000 and not (stats.currenthealth == 0 and stats.currentmana == 0)) or sk.lowwillpower then
      sk.checkwillpower()
    end

    if (affs.blackout and not ((lifevision.l.touchtree_misc and lifevision.l.touchtree_misc.arg == 'blackout') or lifevision.l.blackout_waitingfor)) or (affs.recklessness and not actions.recklessness_focus and not actions.recklessness_herb) then
      local assumestats = conf.assumestats/100
      stats.currenthealth, stats.currentmana =
        math.floor(stats.maxhealth * assumestats), math.floor(stats.maxmana * assumestats)
    end

    -- see what max values changed, update other info accordingly
    if temp.maxhealth ~= stats.maxhealth  or stats.maxhealth == nil then
      signals.changed_maxhealth:emit(temp.maxhealth, stats.maxhealth)
    end
    if temp.maxmana ~= stats.maxmana  or stats.maxmana == nil then
      signals.changed_maxmana:emit(temp.maxmana, stats.maxmana)
    end
  end)

  if not s then
    echoLink("(e!)", [[echo([=[The problem was: prompt vitals function failed - (]]..tostring(m)..[[). Maybe the system isn't installed yet?]=])]], 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a copy/paste of what you saw.')
  end
end


function svo.QQ()
  signals.quit:emit()
end

svo.savesettings = svo.QQ

-- add in blackout only, otherwise go off the prompt - this allows for time tracking
function svo.goteq()
  sys.balancetick = sys.balancetick + 1
  if sys.actiontimeoutid then
    killTimer(sys.actiontimeoutid)
    sys.actiontimeoutid = false
  end

  if sys.misseddisrupt then killTimer(sys.misseddisrupt); sys.misseddisrupt = nil end
  sys.extended_eq = nil

  if affs.blackout and not bals.equilibrium then bals.equilibrium = true; raiseEvent("svo got balance", 'equilibrium') end
end

function svo.gotbalance()
  if affs.blackout then bals.balance = true end
  sys.balancetick = sys.balancetick + 1
  if sys.actiontimeoutid then
    killTimer(sys.actiontimeoutid)
    sys.actiontimeoutid = false
  end

  -- FIXME
  if affs.blackout and not bals.balance then bals.balance = true; raiseEvent("svo got balance", 'balance') end
end

function svo.gotarmbalance()
  sys.balancetick = sys.balancetick + 1
  if sys.actiontimeoutid then
    killTimer(sys.actiontimeoutid)
    sys.actiontimeoutid = false
  end
end

signals["svo lost balance"]:connect(function(balance)
  if balance ~= 'equilibrium' or not conf.noeqtimeout or conf.noeqtimeout == 0 or conf.serverside then return end

  if sys.misseddisrupt then killTimer(sys.misseddisrupt) end
  sys.misseddisrupt = tempTimer(conf.noeqtimeout, function()
    if not bals.equilibrium and not sys.extended_eq and not svo.innews and not affs.disrupt then
      svo.addaffdict(svo.dict.disrupt)
      if not me.passive_eqloss then
        svo.echof("didn't get eq back in %ss - assuming disrupt", tostring(conf.noeqtimeout))
      else
        svo.echof("didn't get eq back in %ss - assuming disrupt and confusion", tostring(conf.noeqtimeout))
        svo.addaffdict(svo.dict.confusion)
      end

      svo.make_gnomes_work()
    end
  end)
end, "disrupt/confusion detection")

signals["svo got balance"]:connect(function(balance)
  if balance ~= 'equilibrium' then return end

  if affs.disrupt then svo.rmaff('disrupt') end
end, "disrupt gone detection")

if svo.haveskillset('weaponmastery') then
signals["svo got balance"]:connect(function(balance)
  if balance ~= 'balance' then return end

  sk.didfootingattack = false
end, "footingattack")
end

-- set a flag that we shouldn't assume disrupt on long-eq actions
function svo.extended_eq()
  sys.extended_eq = true
end

function svo.cnrl.update_siphealth()
  if conf.siphealth then sys.siphealth                   = math.floor(stats.maxhealth * (conf.siphealth/100)) end
  if conf.mosshealth then sys.mosshealth                 = math.floor(stats.maxhealth * (conf.mosshealth/100)) end
  if conf.transmuteamount then sys.transmuteamount       = math.floor(stats.maxhealth * (conf.transmuteamount/100)) end
  if conf.corruptedhealthmin then sys.corruptedhealthmin = math.floor(stats.maxhealth * (conf.corruptedhealthmin/100)) end
if svo.haveskillset('devotion') then
  if conf.bloodswornoff then sys.bloodswornoff           = math.floor(stats.maxhealth * (conf.bloodswornoff/100)) end
end
end
signals.changed_maxhealth:connect(cnrl.update_siphealth, "cnrl.update_siphealth")

function svo.cnrl.update_sipmana()
  if conf.sipmana then sys.sipmana = math.floor(stats.maxmana * (conf.sipmana/100)) end
  if conf.mossmana then sys.mossmana = math.floor(stats.maxmana * (conf.mossmana/100)) end

  sys.manause = math.floor(stats.maxmana * (conf.manause/100))
end
signals.changed_maxmana:connect(cnrl.update_sipmana, "cnrl.update_sipmana")


function svo.cnrl.update_wait()
  sys.wait = svo.wait_tbl[conf.lag].n
end

svo.can_usemana = function()
  return stats.currentmana > sys.manause and
    not svo.doingaction('nomana') -- pseudo-tracking for blackout and recklessness
    and (stats.wp or 0) > 1
end


svo.cnrl.warnids = {}

-- tbl: {initialmsg = "", prefixwarning = "", startin = 0, duration = 0}
function svo.givewarning(tbl)
  svo.checkaction(svo.dict.givewarning.happened, true)

  if conf.aillusion then
    lifevision.add(actions.givewarning_happened.p, nil, tbl, 1)
  else
    lifevision.add(actions.givewarning_happened.p, nil, tbl)
  end
end
function svo.givewarning_multi(tbl)
  svo.checkaction(svo.dict.givewarning.happened, true)
  lifevision.add(actions.givewarning_happened.p, nil, tbl)
end

svo.prefixwarning = function ()
  local deselect, echo, setFgColor = deselect, echo, setFgColor

  if conf.warningtype == 'right' then
    local currentline = getCurrentLine()
    deselect()
    echo(string.rep(" ", conf.screenwidth - #currentline - #cnrl.warning-3))
    setFgColor(0, 050, 200)
    echo("(")
    setFgColor(128, 128, 128)
    echo(cnrl.warning)
    setFgColor(0, 050, 200)
    echo(")")
    moveCursorEnd()
    resetFormat()
  else
    local currentline = getLineCount()
    deselect()
    moveCursor('main', 0, currentline)
    setFgColor(0, 050, 200)
    insertText("(")
    moveCursor('main', 1, currentline)
    setFgColor(128, 128, 128)
    insertText(cnrl.warning)
    moveCursor('main', 1+#cnrl.warning, currentline)
    setFgColor(0, 050, 200)
    insertText(")")
    moveCursor('main', 2+#cnrl.warning, currentline)
    setFgColor(0,0,0)
    insertText(" ")
    moveCursorEnd()
    resetFormat()
  end
end

svo.cnrl.lockdata = {
  ['soft'] = function () return (affs.slickness and affs.anorexia and affs.asthma) end,
  ['venom'] = function () return (affs.slickness and affs.anorexia and affs.asthma and affs.paralysis) end,
  ['hard'] = function () return (affs.slickness and affs.anorexia and affs.asthma and (affs.impatience or (not svo.can_usemana() or not svo.conf.focus))) end,
  ['dragon'] = function () return (defc.dragonform and affs.slickness and affs.anorexia and affs.asthma and (affs.impatience or (not svo.can_usemana() or not svo.conf.focus)) and affs.recklessness and affs.weakness) end,
  ['stain'] = function() return (affs.stain and affs.slickness and ((affs.crippledleftarm or affs.mangledleftarm or affs.mutilatedleftarm) and (affs.crippledrightarm or affs.mangledrightarm or affs.mutilatedrightarm)) and pipes.valerian.puffs == 0) end,
  ['rift'] = function() return ((affs.asthma and (rift.invcontents.kelp == 0 and rift.invcontents.aurum == 0)) and affs.slickness and ((affs.crippledleftarm or affs.mangledleftarm or affs.mutilatedleftarm) and (affs.crippledrightarm or affs.mangledrightarm or affs.mutilatedrightarm))) end,
  ["rift 2"] = function() return (affs.asthma and affs.slickness and affs.anorexia and (affs.paralysis or (affs.disrupt and not bals.equilibrium)) and ((affs.crippledleftarm or affs.mangledleftarm or affs.mutilatedleftarm) and (affs.crippledrightarm or affs.mangledrightarm or affs.mutilatedrightarm))) end,
  ['slow'] = function () return (affs.asthma and affs.slickness and ((affs.crippledleftarm or affs.mangledleftarm or affs.mutilatedleftarm) and (affs.crippledrightarm or affs.mangledrightarm or affs.mutilatedrightarm)) and (affs.mildconcussion or affs.seriousconcussion) and affs.aeon) end,
  ['true'] = function () return (affs.slickness and affs.anorexia and affs.asthma and affs.paralysis and affs.impatience and affs.disrupt and affs.confusion) end
}

--[[ cnrl.checkwarning gets unblocked whenever we receive an aff that is
      included in any of the locks. If you have a lock, it enables the
      cnrl.checkgreen flag, and unblocks dowarning, which allows powercure
      to run and do it's thing. Post processing, cnrl.dolockwarning is run,
      notifying the user on the prompt of any locks (and if any of them
      are in the process of being cured, highlight the lock name in green).

      When we don't have a lock, checkwarning disables itself, the flag,
      and dowarning]]

cnrl.warnings = {}
cnrl.checkwarning = function ()
  cnrl.warnings = {}
  me.locks = {}
  local t = cnrl.warnings
  for lock, func in pairs(cnrl.lockdata) do
    if func() then t[#t+1] = lock; me.locks[lock] = true end
  end

  if not cnrl.checkgreen and #t > 0 then
    signals.after_prompt_processing:unblock(cnrl.dolockwarning)
    cnrl.checkgreen = true
  elseif cnrl.checkgreen and #t == 0 then
    cnrl.checkgreen = false
    signals.after_lifevision_processing:block(cnrl.checkwarning)
    signals.after_prompt_processing:block(cnrl.dolockwarning)
  end
end
signals.after_lifevision_processing:connect(cnrl.checkwarning, "cnrl.checkwarning")
signals.after_lifevision_processing:block(cnrl.checkwarning)

cnrl.dolockwarning = function ()
  local t = cnrl.warnings
  if #t == 1 then
    cecho("<red>(<grey>lock: <orange>" .. t[1].."<red>)")
  elseif #t > 1 then
    cecho("<red>(<grey>locks: <orange>" .. svo.concatand(t).."<red>)")
  else
    -- no more warnings? stop checking for them. Failsafe, we should never get here normally.
    cnrl.checkgreen = false
    signals.after_lifevision_processing:block(cnrl.checkwarning)
    signals.after_prompt_processing:block(cnrl.dolockwarning)
  end
end
signals.after_prompt_processing:connect(cnrl.dolockwarning, "cnrl.dolockwarning")
signals.after_prompt_processing:block(cnrl.dolockwarning)

function svo.cnrl.processcommand(what)
  if not sys.sync or conf.send_bypass then return end

  if conf.blockcommands
  -- and the system is doing something right now...
  and sk.doingstuff_inslowmode()
  -- and this command right here is from you, not the system. Ignore commands starting with 'curing'
  -- though, as those are for serverside and aren't affected
  and not sk.gnomes_are_working and not what:lower():find("^curing") then
    denyCurrentSend()
    if math.random(1,5) == 1 then
      svo.echof("denying <79,92,88>%s%s. Lemme finish!", what, svo.getDefaultColor())
    elseif math.random(1,10) == 1 then
      svo.echof("denying <79,92,88>%s%s. Use tsc to toggle deny mode.", what, svo.getDefaultColor())
    else
      svo.echof("denying <79,92,88>%s%s.", what, svo.getDefaultColor()) end
    return
  elseif not conf.blockcommands and not sk.gnomes_are_working then -- override mode, command from you, not the system

    -- kill old timer first
    if not svo.sacid then svo.echof("pausing curing for your commands.") end

    if svo.sacid then killTimer(svo.sacid) end
    svo.sacid = tempTimer(svo.syncdelay() + getNetworkLatency() + conf.sacdelay, function ()
      svo.sacid = false
      if sys.sync then svo.echof("resuming curing.") end
      svo.make_gnomes_work()
    end)
  end

  -- retardation detection: works by setting a timer off a command, if the timer isn't already set
  -- then when the sluggish msg is seen, the timer is cleared.
  -- amnesia screws with it by hiding the sluggish msg itself!
  if not sk.sluggishtimer and not affs.amnesia and what ~= "" and not what:lower():find("^curing") then
    sk.sawsluggish = getLastLineNumber('main')
    local time = sys.wait + svo.syncdelay() + getNetworkLatency()
    sk.sluggishtimer = tempTimer(time, function ()
      if type(sk.sawsluggish) == 'number' and sk.sawsluggish ~= getLastLineNumber('main') and (affs.retardation or svo.affsp.retardation) then
        if affs.retardation then echo"\n" svo.echof("Retardation seems to have went away.") end
        svo.rmaff('retardation')
      end

      sk.sluggishtimer = nil
    end)
  end
end

signals.sysdatasendrequest:connect(cnrl.processcommand, "cnrl.processcommand")
signals.sysdatasendrequest:block(cnrl.processcommand)

-- parse things for acceptance. ideas to prevent looping: either debug.traceback() (very slow it turned out), or block/unblock handler when doing sys actions (solution used)
function svo.cnrl.processusercommand(what, now)
  -- remove spaces, as some people may use spaces, ie "bedevil " instead of just 'bedevil' which then confuses tracking
  what = what:trim()

  -- if this is a system command done outside of a cnrl.processusercommand block because of batching, catch it
  if sk.systemscommands[what] then svo.debugf("ignoring %s, it's actually a system command", what) return end

  -- debugf("sys.input_to_actions: %s", pl.pretty.write(pl.tablex.keys(sys.input_to_actions)))
  -- debugf("sk.systemscommands: %s", pl.pretty.write(sk.systemscommands))

  if not svo.innews and (what == 'qq' or what == 'quit') then
    svo.QQ()
    svo.echof("Going into empty defs mode so pre-cache doesn't take anything out, and stuffing away all riftables...")
    defs.switch('empty')
    svo.inra()
  elseif not svo.innews and (what == 'ir' or what == "info rift") then
    me.parsingrift = 'all'
  elseif not svo.innews and (what == "ir herb" or what == "ir plant") then -- missing info rift variants
    me.parsingrift = 'herbs'
  elseif not svo.innews and (what == "ir mineral") then
    me.parsingrift = 'minerals'

  elseif sys.input_to_actions[what] then
    local function dostuff()
      svo.killaction(sys.input_to_actions[what])
      local oldsend, oldsendc, oldsendAll = _G.send, svo.sendc, _G.sendAll
      _G.send = function() end
      svo.sendc = function() end
      _G.sendAll = function() end
      local s,m = pcall(svo.doaction, sys.input_to_actions[what])
      if not s then
        echoLink("(e!)", [[svo.echof([=[The problem was: re-mapping commands to system actions failed: (]]..tostring(m)..[[)]=])]], 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a copy/paste of what you saw.')
      end
      _G.send = oldsend
      svo.sendc = oldsendc
      _G.sendAll = oldsendAll
    end

    -- when 'now' is given, put it into queue right away - this is useful for capturing the game curing command
    if now then dostuff() else tempTimer(0, dostuff) end
  end
end
signals.sysdatasendrequest:connect(cnrl.processusercommand, "cnrl.processusercommand")

-- limited_around: don't show the full list, but only 13 elements around the center one
function svo.printorder(balance, limited_around)
  -- translate the obvious 'balance' to 'physical'
  if balance == 'balance' then balance = 'physical' end
  local sendf = svo.errorf
  svo.assert(type(balance) == 'string', "svo.printorder: what balance do you want to print for?", sendf)

  -- get into table...
  local data = svo.make_prio_table(balance)
  local orderly = {}

  -- get a sorted list of just the prios
  for i,_ in pairs(data) do
    orderly[#orderly+1] = i
  end

  table.sort(orderly, function(a,b) return a>b end)

  -- locate where the center of the list is, if we need it
  local center
  if limited_around then
    local counter = 1
    for _, j in pairs(orderly) do
      if j == limited_around then center = counter break end
      counter = counter +1
    end
  end

  svo.echof("%s balance priority list (<112,112,112>clear gaps%s):", balance:title(), svo.getDefaultColor())
  if selectString("clear gaps", 1) ~= -1 then
    setLink("svo.prio.cleargaps('"..balance.."', true)", "Clear all gaps in the "..balance.." balance")
  end

  if not limited_around then
    svo.echof("Stuff at the top will be cured first, if it's possible to cure it.")
  end

  local list = svo.prio.getsortedlist(balance)
  local temp_affs, temp_defs = sk.splitdefs(balance, list)
  local raffs, rdefs = {}, {}
  for index, aff in pairs(temp_affs) do raffs[aff] = index end
  for index, def in pairs(temp_defs) do rdefs[def] = index end

  if limited_around then
    svo.echofn("(")
    setFgColor(unpack(svo.getDefaultColorNums))
    setUnderline(true)
    echoLink("...", "svo.printorder('"..balance.."')", "Click to view the full "..balance.." priority list", true)
    setUnderline(false)
    echo(")\n")
  end

  local function echoserver(j, raffs, rdefs, balance, ssprioamount)
    if raffs[data[j]] then
      return string.format("ss aff %"..ssprioamount..'s', (raffs[data[j]] <= 25 and raffs[data[j]] or '25'))
    elseif rdefs[data[j]] then
      return string.format("ss def %"..ssprioamount..'s', (rdefs[data[j]] <= 25 and rdefs[data[j]] or '25'))
    elseif svo.dict[data[j]][balance].def then
      return "ss def"..(' '):rep(ssprioamount).."-"
    elseif svo.dict[data[j]][balance].aff then
      return "ss aff"..(' '):rep(ssprioamount).."-"
    else
      return "ss    "..(' '):rep(ssprioamount).."-"
    end
  end

  local counter = 1
  local intlen = svo.intlen
  local prioamount = intlen(table.size(orderly))
  local ssprioamount = intlen(table.size(raffs) and table.size(raffs) or table.size(rdefs))
  for _,j in pairs(orderly) do
    if not limited_around or not (counter > (center+6) or counter < (center-6)) then
      setFgColor(255,147,107) echo"  "
      echoLink("^^", 'svo.prio_swap("'..data[j]..'", "'..balance..'", '..(j+1)..', nil, false, svo.printorder, "'..balance..'", '..(j+1)..')', 'shuffle '..data[j]..' up', true)
      echo(" ")
      setFgColor(148,148,255)
      echoLink('vv', 'svo.prio_swap("'..data[j]..'", "'..balance..'", '..(j-1)..', nil, false, svo.printorder, "'..balance..'", '..(j-1)..')', 'shuffle '..data[j]..' down', true)
      setFgColor(112,112,112)
      -- focus balance can't have 'priority'
      if not conf.serverside or balance == 'focus' then
        echo(string.format(" (%s) "..(' '):rep(prioamount - intlen(j)).."%s", j, data[j]))
      else
        -- defs not on defup/keepup won't have a priority
        echo(string.format(" (svo %"..prioamount.."s|%s) %s", j, echoserver(j, raffs, rdefs, balance, ssprioamount), data[j]))
      end
      echo("\n")
      resetFormat()
    end

    counter = counter + 1
  end

  svo.showprompt()
end

function svo.printordersync(limited_around)
  -- step 1: get into table...
  local data = svo.make_sync_prio_table("%s (%s)")
  local orderly = {}

  for i,_ in pairs(data) do
    orderly[#orderly+1] = i
  end

  table.sort(orderly, function(a,b) return a>b end)

  svo.echof("aeon/retardation priority list (clear gaps):")
  if selectString("clear gaps", 1) ~= -1 then
    setFgColor(112,112,112)
    setLink("svo.prio.cleargaps('slowcuring', true)", "Clear all gaps in the aeon/retardation priority")
    resetFormat()
  end

  -- locate where the center of the list is, if we need it
  local center
  if limited_around then
    local counter = 1
    for _, j in pairs(orderly) do
      if j == limited_around then center = counter break end
      counter = counter +1
    end
  end

  if not limited_around then
    svo.echof("Stuff at the top will be cured first, if it's possible to cure it.")
  end

  if limited_around then
    svo.echofn("(")
    setFgColor(unpack(svo.getDefaultColorNums))
    setUnderline(true)
    echoLink("...", "svo.printordersync()", "Click to view the full aeon/retardation priority list", true)
    setUnderline(false)
    echo(")\n")
  end

  local counter = 1
  for _,j in pairs(orderly) do
    if not limited_around or not (counter > (center+6) or counter < (center-6)) then
      setFgColor(255,147,107) echo"  "
      echoLink("^^", 'svo.prio_slowswap("'..string.format("%s_%s", string.match(data[j], "(%w+) %((%w+)%)"))..'", '..(j+1)..', false, svo.printordersync, '..(j+1)..')', 'shuffle '..data[j]..' up', true)
      echo(" ")
      setFgColor(148,148,255)
      echoLink('vv', 'svo.prio_slowswap("'..string.format("%s_%s", string.match(data[j], "(%w+) %((%w+)%)"))..'", '..(j-1)..', false, svo.printordersync, '..(j-1)..')', 'shuffle '..data[j]..' up', true)
      setFgColor(112,112,112)
      echo(" (" .. j..") "..data[j])
      echo("\n")
      resetFormat()
    end

    counter = counter + 1
  end

  svo.showprompt()
end

function svo.sk.check_fullstats()
  if stats.currenthealth >= stats.maxhealth and stats.currentmana >= stats.maxmana and not affs.recklessness then
    sk.gettingfullstats = false
    signals.after_prompt_processing:disconnect(sk.check_fullstats)
    tempTimer(0, function() svo.echof("We're fully healed up now.") svo.showprompt() end)
    raiseEvent("svo got fullstats")

    if sk.fullstatsunignorehp then
      sk.fullstatsunignorehp = nil
      svo.serverignore.healhealth = true
    end

    if sk.fullstatsunignoremp then
      sk.fullstatsunignoremp = nil
      svo.serverignore.healmana = true
    end

    if type(sk.fullstatscallback) == 'function' then
      local s,m = pcall(sk.fullstatscallback)
      if not s then svo.echof("Your fullstats function had a problem:\n  %s", m) end
    elseif type(sk.fullstatscallback) == 'string' then
      local s,m = pcall(loadstring(sk.fullstatscallback))
      if not s then svo.echof("Your fullstats code had a problem:\n  %s", m) end
    end
    sk.fullstatscallback = nil
  end
end


function svo.fullstats(newstatus, callback, echoback)
  if newstatus then
    if stats.currenthealth >= stats.maxhealth and stats.currentmana >= stats.maxmana then
      if echoback then svo.echof("We're already completely healthy.") end
      raiseEvent("svo got fullstats")

      if newstatus and type(callback) == 'function' then
        local s,m = pcall(callback)
        if not s then svo.echof("Your fullstats function had a problem:\n  %s", m) end
      elseif newstatus and type(callback) == 'string' then
        local s,m = pcall(loadstring(callback))
        if not s then svo.echof("Your fullstats code had a problem:\n  %s", m) end
      end
      return
    else

      sk.gettingfullstats = true

      -- if serverside is on, take healhealth off ignore (if it's there) and let it sip up
      if conf.serverside then
        if svo.serverignore.healhealth then
          sk.fullstatsunignorehp = true
          svo.serverignore.healhealth = nil
        end

        if svo.serverignore.healmana then
          sk.fullstatsunignoremp = true
          svo.serverignore.healmana = nil
        end
      end

      signals.after_prompt_processing:connect(sk.check_fullstats, "sk.check_fullstats")
      sk.fullstatscallback = callback
      if echoback then svo.echof("Healing up to full stats.") end
      raiseEvent("svo started fullstats")
      svo.make_gnomes_work()
    end
  elseif not newstatus then
    sk.gettingfullstats = false
    signals.after_prompt_processing:disconnect(sk.check_fullstats)
    if echoback then svo.echof("Resumed normal health/mana healing.") end
    raiseEvent("svo stopped fullstats")
  end
end

svo.prompttrigger = function (name, func)
  svo.assert(name, "svo.prompttrigger: the name needs to be provided")
  svo.assert(type(func) == 'function' or type(func) == 'nil', "svo.prompttrigger: the second argument needs to be a Lua function or nil")

  sk.onprompt_beforeaction_add(name, func)
end

svo.aiprompt = function (name, func)
  sk.onprompt_beforelifevision_add(name, func)
end

function svo.lyre_step()
  if not (bals.balance and bals.equilibrium) then svo.echof("Don't have balance+eq.") return end

  if not conf.lyre then svo.config.set('lyre', 'on', true) end

  if sys.sync then sk.gnomes_are_working = true end
  svo.conf.paused = false
  raiseEvent("svo config changed", 'paused')

  svo.conf.lyre_step = true
  svo.make_gnomes_work()

  if not actions.lyre_physical then
    svo.doaction(svo.dict.lyre.physical)
  end
  svo.conf.lyre_step = false

  if sys.sync then sk.gnomes_are_working = false end
end

-- capture the incoming values for gmcp balance and eq
signals.gmcpcharvitals:connect(function()
  svo.newbals.balance     = gmcp.Char.Vitals.bal == '1' and true or false
  svo.newbals.equilibrium = gmcp.Char.Vitals.eq == '1' and true or false
end, "new balance detection")

-- feed the curing systems curing command through the system, so it can track actions
-- don't raise a systadasendrequest because that would trigger command deny/override
function svo.curingcommand(command)
  sk.sawcuringcommand = true
  cnrl.processusercommand(command:lower(), true)
  svo.prompttrigger("clear curing command", function() sk.sawcuringcommand = false end)

  if conf.gagservercuring then deleteLine() end
end

-- same as curingcommand, but for actions via queue
function svo.queuecommand(command)
  sk.sawqueueingcommand = true
  cnrl.processusercommand(command:lower(), true)

  svo.prompttrigger("clear queueing command", function() sk.sawqueueingcommand = false end)
end

end -- end of svo controllers loader

if svo.systemloaded then svo.loader.controllers() end
  ```
  
</details>


  <summary>Action System</summary>
  
  
  ```lua
  svo = svo or {}; svo.loader = svo.loader or {}
svo.loader.action = function()

local pl = svo.pl
 -- a map of balances, with a map of actions in each
svo.actions_performed = svo.actions_performed or {}
svo.bals_in_use       = svo.bals_in_use or {}

local actions, dict, affs, sys, syncdelay = svo.actions, svo.dict, svo.affs, svo.sys, svo.syncdelay
local debugf, actions_performed, bals_in_use = svo.debugf, svo.actions_performed, svo.bals_in_use
local sk, echof, make_gnomes_work, conf, signals = svo.sk, svo.echof, svo.make_gnomes_work, svo.conf, svo.signals
local lifevision = svo.lifevision

-- does an action - call this when you'd like to execute an action. The system will setup
-- the timeout failsafes (which flow through into stupidity and lag detection), as well as
-- record actions in per-action, per-balance tables for checking against later
-- ie: doaction(dict.healhealth.sip)
svo.doaction = function(arg1, arg2)
  local act, which, balance
  -- do action used to be available in 2 modes - internal and external. Internal API took a dict[action][balance] and
  -- the external API took 2 strings as the action and balance. This is a compatibility layer for the merged result
  if type(arg1) == 'table' then
    act = arg1
  else
    which = arg1
    balance = arg2
    svo.assert(dict[which], "svo.doaction: "..which.." action doesn't exist. See 'vshow ignorelist' for a list of them.")
    svo.assert(dict[which][balance], "svo.doaction: "..which.." doesn't have a "..balance.. " balance.")
    act = dict[which][balance]
	end
  --it'll be in format of dict.what.#somebalance
  -- add to a table, create timers and store id's in there
  -- if ai is on, enable the relevant triggers <- maybe we should do
  -- it in dict action.

  local expirein =
    affs.seriousconcussion and sys.sync
    and (syncdelay()+.3) -- w/ aeon, waits 1.3s and etc, w/o aeon - 0.3s and etc..
    or ((act.customwait or (act.customwaitf and act.customwaitf()) or 0) + sys.wait) + syncdelay()

  debugf("expirein for %s set to %s", act.name, expirein)

  local timerid = tempTimer(expirein,
    function ()
      debugf("actions: %s timed out", tostring(act.name))
      actions:set(act.name, nil)  -- remove from actions list

      -- it should always be there, even after reset - but a failsafe is in place anyway
      if bals_in_use[act.balance] then
        bals_in_use[act.balance][act.name] = nil
      end
      actions_performed[act.action_name] = nil

      -- there might be a case where actions_performed was being taken up by another action, like a cure,
      -- that was overwritten by an aff now. This needs to be rectified back.
      for bal, _ in pairs(bals_in_use) do
        if actions[act.action_name..'_'..bal] then
          actions_performed[act.action_name] = bals_in_use[bal][act.action_name..'_'..bal]

          debugf("actions: added %s_%s back", act.action_name, bal)
        end
      end

      -- don't need to pause the system itself as all actions timing out will come to this, ie build up,
      -- get disabled until next prompt
      sys.lagcount = sys.lagcount + 1
      if sys.lagcount < (sys.lagcountmax+1) then
        if act.ontimeout then act.ontimeout() end

        -- if we have a stupidity counted and we timed out, then stupidity might be real
        if sk.stupidity_count and sk.stupidity_count > 0 and not affs.stupidity then
          svo.addaffdict(dict.stupidity)
          echof("I suspect we've got stupidity.")
        end

        make_gnomes_work()
      elseif sys.lagcount == (sys.lagcountmax+1) and not conf.paused then
        echof("Warning, lag detected (while doing/curing %s)", act.action_name)
        sk.increase_lagconf()
      end
    end
  )

  -- act.name is a single string of action + balance - ie, bleeding_misc (cure) or bleeding_aff (affliction)
  actions:set(act.name, {
    timerid = timerid,
    completed = function (other_action, arg)
      killTimer(timerid)
      if (other_action) then debugf("doaction other action: %s", tostring(other_action)) end
      if not (act[other_action or 'oncompleted']) then
        debugf("[error] %s does not exist for %s!", tostring(other_action or 'oncompleted'), tostring(act.name))
      end
      act[other_action or 'oncompleted'](arg)
      debugf("actions: %s%s completed (killed %s)", act.name, arg and (' ('..tostring(arg)..')') or "",
        tostring(timerid))

      if act.balance == 'focus' then signals.curedwith_focus:emit(other_action or 'oncompleted') end
    end,
    p = act
  })

  if not (act.balance or act.action_name) then
    debugf("balance: %s, name: %s, what: %s", tostring(act.balance), tostring(act.action_name), tostring(act))
  end

  bals_in_use[act.balance] = bals_in_use[act.balance] or {}
  bals_in_use[act.balance][act.name] = act
  -- ie, bleeding
  actions_performed[act.action_name] = act

  -- lastly, do it! :)

  debugf("actions: doing %s", act.name)
  local s,m = pcall(act.onstart)
  if not s then
    debugf("error from onstart(): "..m)
    echoLink("(e!)", [[echo([=[The problem was: ']]..tostring(act.action_name)..[[ failed to start (]]..
      tostring(m)..[[). If this is curing-related, please include that your curemethod is set to ]]..
      tostring(conf.curemethod)..[["]=])]],
      'Oy - there was a problem. Click this link to show the error')
  else
    -- action started successfully - start the stopwatch for it.
    -- It's accessible via actions.action_balance.p.actionwatch
    act.actionwatch = act.actionwatch or createStopWatch()
    startStopWatch(act.actionwatch)
  end

end

-- used for pre-checking if we're doing an action in trigger functions.
-- if anti-illusion is off, or the true argument is passed, the action
-- will get recorded as currently being done (this helps in cases where a 3rd
-- party did an action on you and you want to record it)
svo.checkaction = function (act, input)
  if not act then debugf("[svo error]: checkaction called with -nothing-") return end
  -- if doesnt exist in table, and we got ai off, make one up
  if not actions[act.name] and ((not conf.aillusion and input ~= false) or input) then

  debugf("actions: force-adding %s", act.name)

    actions:set(act.name, {
      completed = function (other_action, arg)
        if (other_action) then debugf("checkaction other action: %s", other_action) end
        if not (act[other_action or 'oncompleted']) then
          debugf("[error] %s does not exist for %s!", tostring(other_action or 'oncompleted'), tostring(act.name))
        end
        act[other_action or 'oncompleted'](arg)
      debugf("actions: %s%s completed", act.name, arg and tostring(arg) or "")
        if act.balance == 'focus' then signals.curedwith_focus:emit(other_action or 'oncompleted') end
      end,
      p = act,
    })

    bals_in_use[act.balance] = bals_in_use[act.balance] or {}
    bals_in_use[act.balance][act.name] = act
    actions_performed[act.action_name] = act
  end
end

-- checks if any of the actions are being done (multi-param version of checkaction), returns one if true
-- doesn't have ability to force-insert like checkaction
svo.checkany = function (...)
  local t = {...}

  for i=1,#t do
    local j = t[i]
    if not j then debugf("missing %s, traceback: %s", j, debug.traceback()) end
    if actions[j.name] then
      return j
    end
  end
end

-- returns one of the actions currently being done in the given balance
svo.findbybal = function (balance)
  return bals_in_use[balance] and select(2, next(bals_in_use[balance]))
end

-- checks if any of the physical actions being done right now are expected to consume balance
svo.will_take_balance = function()
  bals_in_use.physical = bals_in_use.physical or {}
  for _, data in pairs(bals_in_use.physical) do
    if data.balanceful_act then return true end
  end
end

svo.codepaste.balanceful_codepaste = svo.will_take_balance

-- multi-balance version of findbybal
svo.findbybals = function(balances)
  local t = {}

  for _, bal in ipairs(balances) do
    if bals_in_use[bal] then
      for _, act in pairs(bals_in_use[bal]) do
        t[act.name] = act
      end
    end
  end

  if next(t) then return t end
end

-- only used by lifevision system if an illusion was detected - used by lifevision system to clear actions it has seen
svo.actionclear = function(act)
  debugf("actions: cleared action %s", tostring(act.name))

  actions:set(act.name, nil)
   -- it should always be there, even after reset - but a failsafe is in place anyway
  if bals_in_use[act.balance] then
    bals_in_use[act.balance][act.name] = nil
  end
  actions_performed[act.action_name] = nil

  -- there might be a case where actions_performed was being taken up by another action,
  -- like a cure, that was overwritten by an aff now. This needs to be rectified back.
  for bal, _ in pairs(bals_in_use) do
    if actions[act.action_name..'_'..bal] then
      actions_performed[act.action_name] = bals_in_use[bal][act.action_name..'_'..bal]

      debugf("actions: added %s_%s back", act.action_name, bal)
    end
  end

  if act.oncancel then
    local s,m = pcall(act.oncancel)
    if not s then
      debugf("error from oncancel(): "..m)
      echoLink("(e!)", [[echo([=[The problem was: ']]..tostring(act.action_name)..[[ failed to cancel (]]..
        tostring(m)..[[). If this is curing-related, please include that your curemethod is set to ]]..
        tostring(conf.curemethod)..[["]=])]],
        'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a '..
          'copy/paste of what you saw.')
    end
  end
end

-- used by lifevision system to complete an action that was seen in the paragraph (when no illusion was seen)
svo.actionfinished = function(act, other_action, arg)
  svo.assert(act, "svo.actionfinished wants an argument")
  if not act.name or not actions[act.name] or not actions[act.name].completed then
    echo("(e!)")
    debugf("actionfinished: %s", debug.traceback())
    return
  end

  if not act.name then debugf("svo error: no name field, total: %s", pl.pretty.write(act or {})) return end
  if not actions[act.name] then debugf("svo error: no such action %s being done atm", act.name) return end
  if not actions[act.name].completed then debugf("svo error: no completed method on %s", act.name) return end

  local result, msg = pcall(actions[act.name].completed, other_action, arg)

  if not result then
    debugf("error from completed() "..msg)
    if other_action then debugf("other_action was: %s", other_action) end
    echoLink("(e!)", [[svo.echof([=[The problem was: ']]..tostring(act.action_name)..[[' failed to complete: ]]..msg..[[]=])]]
      , 'Oy - there was a problem. Click on this link and submit a bug report with what it says along with a '..
        'copy/paste of what you saw.')
     end

  actions:set(act.name, nil)
  if bals_in_use[act.balance] then -- it should always be there, even after reset - but a failsafe is in place anyway
    bals_in_use[act.balance][act.name] = nil
  end
  actions_performed[act.action_name] = nil

  if not other_action then debugf("actions: finished action %s", tostring(act.name)) else
  debugf("actions: finished action %s, with non-default action: (%s)", tostring(act.name), other_action) end

  -- there might be a case where actions_performed was being taken up by another action, like a cure,
  -- that was overwritten by an aff now. This needs to be rectified back.
  for bal, _ in pairs(bals_in_use) do
    if actions[act.action_name..'_'..bal] then
      actions_performed[act.action_name] = bals_in_use[bal][act.action_name..'_'..bal]

      debugf("actions: added %s_%s back", act.action_name, bal)
    end
  end

  -- slow curing? kick the next action into going then
  if sys.sync then tempTimer(0, function() make_gnomes_work() end) end
end

-- cancels an action entirely
-- needs the dict+balance, ie: killaction (dict.icing.waitingfor)
svo.killaction = function (act)
  if not (act and act.name) then
    debugf("%s is invalid action to kill.", tostring(act))
    return
  end

  svo.assert(act, "svo.killaction wants an argument")

  if not actions[act.name] then return end

  if act.onkill then act.onkill() end

  if actions[act.name].timerid then
    killTimer(actions[act.name].timerid)
  end
  actions:set(act.name, nil)

  if bals_in_use[act.balance] then -- it should always be there, even after reset - but a failsafe is in place anyway
    bals_in_use[act.balance][act.name] = nil
  end
  actions_performed[act.action_name] = nil

  debugf("actions: killed early %s", tostring(act.name))

  -- there might be a case where actions_performed was being taken up by another action, like a cure,
  -- that was overwritten by an aff now. This needs to be rectified back.
  for bal, _ in pairs(bals_in_use) do
    if actions[act.action_name..'_'..bal] then
      actions_performed[act.action_name] = bals_in_use[bal][act.action_name..'_'..bal]

      debugf("actions: added %s_%s back", act.action_name, bal)
    end
  end

  if lifevision.l[act.name] then
    lifevision.l:set(act.name, nil)
    debugf("actions: also removed it from lifevision")
  end

  -- slow curing? kick the next action into going then
  if sys.sync then tempTimer(0, function() make_gnomes_work() end) end
end

-- returns true if the balance will be used up by an action that was sent
svo.usingbal = function (which)
  return (bals_in_use[which] and next(bals_in_use[which])) and true or false
end

-- usingbal was previously private - this is for historical API compatibility
svo.usingbalance = svo.usingbal

-- slight problem with this - it uses the short name, without the balance/action - so some misc things such as sleep,
--  which can happen at once, are a problem. workaround is to combine doingaction with usingbal in there.
svo.doingaction = function (which)
  svo.assert(which, "svo.doingaction wants an argument")

  return actions_performed[which] and true or false
end

-- doingaction was previously private - this is for historical API compatibility
svo.doing = svo.doingaction

-- String -> Action/Aff/Nil
-- returns true if we currently have or will register (after this prompt and no illusions) an affliction
svo.haveorwill = function (aff)
  return actions[aff..'_aff'] or affs[aff]
end

-- string -> boolean
-- returns true if the given string in the format of actionname_balance exists
svo.valid_sync_action = function(name)
  local actionname, balance = name:match("^(%w+)_(%w+)$")
  if not (actionname and balance) then
    return false, "actionname is in invalid format; it should be as 'actionname_balance'"
  end

  if not dict[actionname] then return false, "action "..actionname.." doesn't exist" end
  if not dict[actionname][balance] then return false, actionname.." doesn't operate on the "..balance.." balance" end

  return true, actionname, balance
end

end -- end of svo action loader

if svo.systemloaded then svo.loader.action() end
  ```
  
</details>



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


  <summary>Fresh Config - (1-8)</summary>
  
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


  <summary>Iniars Doblinker</summary>

  
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



  <summary>Creator</summary>
  
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


  <summary>Heroism</summary>
    
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

