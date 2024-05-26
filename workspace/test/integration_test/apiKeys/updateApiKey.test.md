import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  adminPassword,
  password,
  mockExpireDate,
  mockMaxCount,
} from '../data/apikey.js'
import apiKeyService from '../requiredTest/apiKeyService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'

export const apiKeyUpdate = () => {
  describe('UPDATE APIKEY ', () => {
    let apiKeyId
    let token
    let userToken
    let apiKey
    let userId
    let adminToken
    let adminId
    let superAdminId
    let upd_apiKey

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        const adminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'admin'
        }

        adminId = await usersService.create(adminInfo)

        delete adminInfo.role

        adminToken = await usersService.login(adminInfo)

        const userInfo = {
          username: randomUUID(),
          password: password,
          role: 'user'
        }

        userId = await usersService.create(userInfo)

        delete userInfo.role

        userToken = await usersService.login(userInfo)

        apiKey = {
          UserId: adminId,
          maxCount: mockMaxCount,
          expireDate: mockExpireDate,
        }

        apiKeyId = await apiKeyService.create(apiKey)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await apiKeyService.delete(apiKeyId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    beforeEach(() => {
      upd_apiKey = {
        isValid: true,
        maxCount: mockMaxCount,
        expireDate: mockExpireDate,
      }
    })

    it('update apiKey completed successfully', (done) => {
      try {
        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('isValid must be boolean', (done) => {
      try {
        upd_apiKey.isValid = 7888888

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.IS_VALID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount must be a number', (done) => {
      try {
        upd_apiKey.maxCount = 'tomorrow'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount should not be string', (done) => {
      try {
        upd_apiKey.maxCount = '10.5'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount can not be null value', (done) => {
      try {
        upd_apiKey.maxCount = null

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('isValid can not be null value', (done) => {
      try {
        upd_apiKey.isValid = null

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.IS_VALID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('isValid must be boolean', (done) => {
      try {
        upd_apiKey.isValid = 'correct'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.IS_VALID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        upd_apiKey.UserId = 'kjshdfehjgashfjgashjf'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate must be string', (done) => {
      try {
        upd_apiKey.expireDate = 54646543453453

        console.log(apiKeyId)

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EXPIRE_DATE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate must be started with numbers', (done) => {
      try {
        upd_apiKey.expireDate = 'oneh'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.EXPIRE_DATE_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('between number and the word in expireDate should not be any character, not even a space', (done) => {
      try {
        upd_apiKey.expireDate = '1 h'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.EXPIRE_DATE_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not update a service', (done) => {
      try {
        upd_apiKey.isValid = false

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(upd_apiKey)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apiKeyId is not found', (done) => {
      try {
        let wrongApiKeyId = 'kjh23j4h2jh34'

        request
          .patch(`/api/apikeys/${wrongApiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can update apiKeys', (done) => {
      try {
        upd_apiKey.maxCount = 100

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .send(upd_apiKey)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token = 'kjh23j4h2jh34'

        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .patch(`/api/apikeys/${apiKeyId}`)
          .send(apiKey)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
