Score
=====

Display a score in the upper right of the screen.

    {Util:{defaults}, GameObject} = require "dust"

    module.exports = (I={}) ->
      value = 0

      self = GameObject(I)

      self.on "update", ->
        if player = engine.first("Player")
          value = player.I.score

      self.off "draw"
      self.on "overlay", (canvas) ->
        canvas.centerText
          color: "white"
          font: "24px bold 'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial, 'Lucida Grande', sans-serif"
          text: value
          x: 400
          y: 30

      return self
