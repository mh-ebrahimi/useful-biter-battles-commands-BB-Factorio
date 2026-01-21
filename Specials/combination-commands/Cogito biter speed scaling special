--- Biters accelerate their movement and attack speed based on current match time.
if storage.cbs then
    game.print("Biters speed scaling is active already!")
    return
end

storage.cbs = {
    cycle = 60,
    info = {},
}

function create_description()
  local chest = game.player.surface.create_entity(
    {
      name = "wooden-chest",
      position = { 0, -20 },
    }
  )
  chest.minable = false
  chest.destructible = false
  chest.operable = false

  local texts = {
        '-> Biters speed scaling by cogito <-',
        'Regular biters gain movement and attack speed over the course of a match',
        'Movement speed starts increasing once attack speed reaches 200%',
        'a',
        'b',
        'c'
  }

  for i, text in ipairs(texts) do
        local color = { 255, 200, 0 }
        color = i > 4 and { 221, 160, 221 } or color
        storage.cbs['info'][i] = rendering.draw_text {
          text = text,
          surface = game.player.surface,
          target = { entity = chest, offset = { 0, 1.25 * i } },
          color = color,
          scale = 1.00,
          font = "heading-1",
          alignment = "center",
          scale_with_zoom = true
        }
  end
end

create_description()

local event = require "utils.event"

local adjust_unit_speed = [[function(event)
  local entity = event.unit or event.entity
  if not (entity and entity.valid) then
    return
  end

  if string.find(entity.force.name, "_boss", 1, true) then
    return
  end

  local functions = require("maps.biter_battles_v2.functions")
  local ticks = functions.get_ticks_since_game_start()
  local minutes = ticks / 3600

  local base_spd = entity.prototype.speed
  local factor = math.max(1, minutes / storage.cbs.cycle)
  local spd = base_spd * factor

  entity.speed = spd
end]]

event.add_removable_function(
  defines.events.on_unit_added_to_group,
  adjust_unit_speed,
  "cbs_on_unit_added_to_group"
)

event.add_removable_function(
  defines.events.on_entity_spawned,
  adjust_unit_speed,
  "cbs_on_entity_spawned"
)

local on_tick = [[function(event)
  local functions = require("maps.biter_battles_v2.functions")
  local ticks = functions.get_ticks_since_game_start()
  local minutes = ticks / 3600
  local melee_spd = minutes / storage.cbs.cycle
  local biological_spd = minutes / storage.cbs.cycle
  local forces = {
    "north_biters",
    "south_biters",
  }
  for _, name in ipairs(forces) do
      local f = game.forces[name]
      f.set_gun_speed_modifier("melee", melee_spd)
      f.set_gun_speed_modifier("biological", biological_spd)
  end

  local mov_spd = math.max(1, minutes / storage.cbs.cycle)
  storage.cbs['info'][4].text = string.format('Speed increases by 100%% every %d minutes', storage.cbs.cycle)
  storage.cbs['info'][5].text = string.format('Current movement speed: %.2f%%', mov_spd * 100)
  storage.cbs['info'][6].text = string.format('Current attack speed: %.2f%%', (melee_spd + 1) * 100)
end]]

event.add_removable_function(
  defines.events.on_tick,
  on_tick,
  "cbs_on_tick"
)

local on_surface_deleted = [[function(event)
  storage.cbs = nil

  local event = require "utils.event"
  event.remove_removable_function(defines.events.on_unit_added_to_group, "cbs_on_unit_added_to_group")
  event.remove_removable_function(defines.events.on_entity_spawned, "cbs_on_entity_spawned")
  event.remove_removable_function(defines.events.on_tick, "cbs_on_tick")
  event.remove_removable_function(defines.events.on_surface_deleted, "cbs_on_surface_deleted")
  game.print("Biters speed scaling removed")
end]]

event.add_removable_function(
  defines.events.on_surface_deleted,
  on_surface_deleted,
  "cbs_on_surface_deleted"
)

local p = game.player
game.print("Biters speed scaling enabled by " .. p.name)
