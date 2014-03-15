Surfn
=====

As a lone FBI agent you must surf to survive.

    require "./setup"

    DEBUG_DRAW = false

Get the app size from our config.

    {width, height} = require "/pixie"

    engine.I.backgroundColor = "skyblue"

    setUpGame = ->
      player = engine.add "Player",
        x: 240
        y: 0

      engine.add "Score"

      12.times (n) ->
        engine.add "Cloud",
          x: n * (1600) / 12
          y: ((((n * 351) % 1279) % 240) % 7) * 40

      engine.add "Pipe"

    loadingBar = engine.add
      x: width/2
      y: height/2
      width: 0
      height: height
      color: "white"

    require("/lib/preloader").preload
      resources: [
        "/images"
      ].map require
      progress: (percent) ->
        console.log percent
        loadingBar.I.width = percent * width
      complete: ->
        loadingBar.destroy()
        setUpGame()

    require("/lib/preloader").softPreload [
      "/music"
      "/sounds"
    ].map require

    # TODO: This should be simpler like engine.follow("Player")
    ###
    camera = engine.camera()
    camera.on "afterUpdate", ->
      if player = engine.find("Player").first()
        camera.I.transform.tx = 240 + player.I.x
    ###

    # TODO: This is a stupid hack because I haven't fixed the cameras yet
    engine.on "afterUpdate", ->
      if player = engine.find("Player").first()
        deltaX = width/2 - player.I.x

        player.I.distance -= deltaX

        engine.objects().forEach (object) ->
          object.I.x += deltaX

    clock = 0
    engine.on "update", ->
      clock += 1

    restartGame = ->
      engine.objects().invoke "destroy"

      doRestart = ->
        engine.unbind "afterUpdate", doRestart
        setUpGame()

      engine.on "afterUpdate", doRestart

    engine.on "draw", (canvas) ->
      if DEBUG_DRAW
        engine.find("Player, Rock").invoke("trigger", "drawDebug", canvas)

    engine.bind "restart", ->
      restartGame()

    music = require "./music"
    Music.playFromURL music["SurfN-2-Sur5"]

    engine.start()

    # Meta controls
    $(document).on "keydown", null, "pause", ->
      engine.pause()
