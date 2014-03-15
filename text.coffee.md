Text
====

Display a score in the upper right of the screen.

    {Util:{defaults}, GameObject} = require "dust"

    module.exports = (I={}) ->
      defaults I,
        duration: 2

      self = GameObject(I)

      self.off "draw"
      self.on "draw", (canvas) ->
        canvas.font "18px bold 'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial, 'Lucida Grande', sans-serif"

        canvas.centerText
          color: "black"
          text: I.text
          x: -1
          y: 1
          
        canvas.centerText
          color: "purple"
          text: I.text
          x: 0
          y: 0

      return self
