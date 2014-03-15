Player
======

    {Util:{defaults}} = require "dust"

    Base = require "./base"

    module.exports = (I={}) ->
      defaults I,
        airborne: true
        launchedAt: 0
        landedAt: 0
        distance: 0
        heading: Math.TAU / 4
        spriteName: "player"
        launchBoost: 1.5
        radius: 8
        rotationVelocity: Math.TAU / 64
        rotationAccumulator: 0
        waterSpeed: 5
        score: 0
        velocity: Point(0, 0)
        zIndex: 5

      self = Base(I)

      GRAVITY = Point(0, 0.25)

      sprites = []
      angleSprites = 8
      angleSprites.times (n) ->
        t = n * 2
        sprites.push "player_#{t}"

      setSprite = ->
        n = (angleSprites * I.heading / Math.TAU).round().mod(angleSprites)

        I.spriteName = sprites[n]

      wipeout = (causeOfDeath) ->
        I.active = false

        Sound.play("crash")

        engine.add
          class: "GameOver"
          causeOfDeath: causeOfDeath
          distance: I.distance
          time: I.age
          y: 160

      land = () ->
        projection = self.velocity().norm().dot(Point.fromAngle(I.heading))

        if projection < 0
          wipeout("bad landing")

        I.airborne = false

        Sound.play("land")

      score = (name, value=0) ->
        I.score += value

        engine.add "Text",
          text: name
          x: I.x
          y: I.y

      launch = () ->
        I.airborne = true
        I.velocity = I.velocity.norm(I.launchBoost * I.waterSpeed)

        if (I.age - I.launchedAt) < 0.25
          score "5-0", 1
        else
          score "Launchin!"

        I.launchedAt = I.age

        Sound.play("splash")
        

      self.on "drawDebug", (canvas) ->
        canvas.strokeColor("rgba(0, 255, 0, 0.75)")

        p = Point.fromAngle(I.heading).scale(10)
        canvas.drawLine
          start: Point(I.x - p.x, I.y - p.y)
          end: Point(I.x + p.x, I.y + p.y, 1)

      self.on "update", ->
        I.x += I.velocity.x
        I.y += I.velocity.y

        circle = self.circle()
        hitRock = false
        engine.find("Rock").each (rock) ->
          if Collision.circular circle, rock.circle()
            hitRock = true

        if hitRock
          wipeout("a rock")
          return

        pipe = engine.find(".pipe").first()
        waterLevel = pipe.top()
        depthsLevel = pipe.bottom()

        headingChange = I.rotationVelocity
        headingChange *= 2 if I.airborne

        if keydown.left
          I.heading -= headingChange
          
          if I.airborne
            I.rotationAccumulator += headingChange

        if keydown.right
          I.heading += headingChange

          if I.airborne
            I.rotationAccumulator += headingChange
        
        if I.rotationAccumulator > Math.TAU
          I.rotationAccumulator -= Math.TAU
          score "360", 10

        if I.rotationAccumulator < -Math.TAU
          I.rotationAccumulator += Math.TAU
          score "360", 10

        setSprite()

        if I.y > depthsLevel
          wipeout("the depths")
        else if I.y >= waterLevel
          if I.airborne
            land()
            I.rotationAccumulator = 0

          speed = I.velocity.magnitude()

          speed = speed.approachByRatio(I.waterSpeed, 0.1)

          I.velocity = Point.fromAngle(I.heading).scale(speed)
        else
          if !I.airborne
            launch()

          I.velocity = I.velocity.add(GRAVITY)

      return self
