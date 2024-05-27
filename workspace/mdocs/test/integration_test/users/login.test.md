import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)

import {
  mockUsername,
  mockPassword,
  mockRole,
} from '../data/user.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'


export const userLogin = () => {
  describe('LOGIN ', () => {
    let loginInfo
    let userId
    let userInfo

    beforeAll(async () => {
      try {
        userInfo = {
          username: mockUsername,
          password: mockPassword,
          role: mockRole,
        }

        userId = await usersService.create(userInfo)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await usersService.delete(userId)
    })

    beforeEach(() => {
      loginInfo = {
        username: mockUsername,
        password: mockPassword,
      }
    })

    it('checkServerHealth  ------ statuscode = 200 ', (done) => {
      try {
        request
          .get('/api/checkServerHealth')
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: 'ok',
              })
            )
            return done()
          })
      } catch (error) {}
    })


    it('LOGIN  ------ statuscode = 200', (done) => {
      try {
        request
          .post('/api/users/login')
          .send(loginInfo)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  token: res.body.data.message.token,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('password can not be empty', (done) => {
      loginInfo.password = ''
      request
        .post('/api/users/login')
        .send(loginInfo)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
            })
          )
          return done()
        })
    })

    it('username field can not be empty', (done) => {
      loginInfo.username = ''

      request
        .post('/api/users/login')
        .send(loginInfo)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.c.USERNAME} ${messages.m.EMPTY}`,
            })
          )
          return done()
        })
    })

    it('username does not exist', (done) => {
      loginInfo.username = 'AliAbdiAdmin1'

      request
        .post('/api/users/login')
        .send(loginInfo)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.m.USER_NOT_FOUND}`,
            })
          )
          return done()
        })
    })

    it('username must be string', (done) => {
      loginInfo.username = 87876874564

      request
        .post('/api/users/login')
        .send(loginInfo)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
            })
          )
          return done()
        })
    })

    it('password is not correct', (done) => {
      loginInfo.password = 'aliA123@B'

      request
        .post('/api/users/login')
        .send(loginInfo)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.m.INVALID_LOGIN}`,
            })
          )
          return done()
        })
    })
  })
}
