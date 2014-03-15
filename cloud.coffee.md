Cloud
=====

Just floating along in the background.

    {Util:{defaults}} = require "dust"

    Base = require "./base"

    module.exports = (I) ->
      defaults I,
        spriteName: "cloud"
        height: 32
        width: 128
        y: 240
        zIndex: 0

      self = Base(I)

      self.on "update", ->
        if I.x < -400
          I.x += 1600
        
        if I.x > 1200
          I.x -= 1600

      self
