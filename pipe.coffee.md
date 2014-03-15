Half Pipe
=========

    {Util:{defaults}, GameObject} = require "dust"
    
    {width, height} = require "/pixie" 

    module.exports = (I={}) ->
      defaults I,
        color: "white"
        pipe: true
        x: 0
        y: height * 3 / 4
        width: width
        height: height/2
        zIndex: 1

      self = GameObject(I)

      self.attrAccessor "pipe"

      self.on "update", ->
        if player = engine.find("Player").first()
          I.x = player.I.x
        else
          I.x = width/2

      self.on "draw", (canvas) ->
        offset = I.x.mod(32)

      return self
