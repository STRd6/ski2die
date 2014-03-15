Setup
=====

Require a bunch of junk and throw in some hacks for good measure.

Expose package for debugging

    global.PACKAGE = PACKAGE

    # TODO: Maybe jQuery should move into Dust since that's what depends on it
    require "jQuery"

    {Util:{applyStylesheet}} = require "dust"
    applyStylesheet(require("/style"), "primary")

Use the Dust game engine.

    Dust = require "dust"

    # TODO: Clean up globals
    global.Observable = require "observable" # HACK: Needed for HamlJr runtime right now
    
    global.Collision = Dust.Collision

    Audio = require "audio"
    global.Sound = Audio.Sound
    global.Music = Audio.Music

    # TODO: Fix this up a bit, merge into a resource manager
    Sound.play = (name) ->
      sounds = require "/sounds"

      Sound.playFromURL(sounds[name])

    require "/duct_tape"

    require "./engine/options"

Create the engine.

    {width, height} = require "/pixie"

    global.engine = Dust.init
      width: width
      height: height

Register our GameObjects.

    engine.register "Cloud", require "./cloud"
    engine.register "GameOver", require "./game_over"
    engine.register "Player", require "./player"
    engine.register "Pipe", require "./pipe"
    engine.register "Score", require "./score"
