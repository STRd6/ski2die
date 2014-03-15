Water
=====

    {Util:{defaults}, GameObject} = require "dust"
    
    {width, height} = require "/pixie" 

    module.exports = (I={}) ->
      defaults I,
        color: "white"
        pipe: true
        x: 0
        y: 240
        width: width
        height: height/2
        zIndex: 1

      depthsSprites = [Sprite.loadByName("depths0"), Sprite.loadByName("depths1")]

      self = GameObject(I)

      self.attrAccessor "pipe"

      self.on "update", ->
        if player = engine.find("Player").first()
          I.x = player.I.x

      self.on "draw", (canvas) ->
        offset = I.x.mod(32)

        canvas.withTransform Matrix.translation(-I.width/2, 0), (canvas) ->
          depthsSprites.wrap((I.age / 8).floor()).fill(canvas, 0, I.height/2, I.width, I.height)

      return self
