Powerup
=======

    {Util:{defaults}} = require "dust"

    Base = require "./base"

    {width, height} = require "./pixie"

    module.exports = (I={}) ->
      defaults I,
        color: "yellow"
        duration: 10
        width: 10
        height: 10
        x: rand(width - 100) + 50
        y: rand(height - 100) + 50
        zIndex: 10

      self = Base(I)
      
      self.on "update", ->
        if player = engine.first("Player")
          if self.position().distance(player.position()) < 20
            self.destroy()
            player.speedUp(1)


      return self
